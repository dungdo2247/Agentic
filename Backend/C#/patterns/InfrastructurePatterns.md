# Infrastructure Patterns

## Memory Cache Service

`GetOrCreateAsync` prevents cache stampedes.
Location: `Infrastructure/Caches/`

```csharp
using Microsoft.Extensions.Caching.Memory;

public class {Entity}CacheService
{
    private readonly IMemoryCache _cache;
    private readonly I{Entity}Repository _repository;
    private const string CacheKey = "{Entity}_List";

    public {Entity}CacheService(IMemoryCache cache, I{Entity}Repository repository)
    {
        _cache = cache;
        _repository = repository;
    }

    public async Task<IReadOnlyList<{Entity}Dto>> GetAllAsync(CancellationToken ct)
        => await _cache.GetOrCreateAsync(CacheKey, async entry =>
        {
            entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromHours(12);
            return await _repository.GetAllActiveAsync(ct);
        }) ?? [];
}
```

---

## Refit HTTP Client with Auth Interceptor

Use `DelegatingHandler` to inject Bearer tokens cleanly.
Location: `Infrastructure/Services/`

```csharp
using System.Net.Http.Headers;

public class AuthHeaderHandler : DelegatingHandler
{
    private readonly ITokenService _tokenService;

    public AuthHeaderHandler(ITokenService tokenService)
        => _tokenService = tokenService;

    protected override async Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
    {
        var token = await _tokenService.GetAccessTokenAsync(cancellationToken);
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", token);
        return await base.SendAsync(request, cancellationToken);
    }
}
```

---

## EF Core Entity Configuration

Keep mapping out of entity classes - use `IEntityTypeConfiguration<T>`.
Location: `Infrastructure/Persistence/Configurations/`

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;

public class {Entity}Configuration : IEntityTypeConfiguration<{Entity}>
{
    public void Configure(EntityTypeBuilder<{Entity}> builder)
    {
        builder.ToTable("{Entity}s");
        builder.HasKey(e => e.Id);
        builder.Property(e => e.Name).IsRequired().HasMaxLength(200);
        builder.Property(e => e.CreatedBy).IsRequired().HasMaxLength(200);
        builder.Property(e => e.UpdatedBy).IsRequired().HasMaxLength(200);
    }
}
```

---

## Repository Implementation

`.AsNoTracking()` on all reads. Hide `DbContext` from Application.
Location: `Infrastructure/Persistence/Repositories/`

```csharp
using Microsoft.EntityFrameworkCore;

public class {Entity}Repository : I{Entity}Repository
{
    private readonly ApplicationDbContext _context;

    public {Entity}Repository(ApplicationDbContext context)
        => _context = context;

    public async Task<{Entity}?> GetByIdAsync(Guid id, CancellationToken ct)
        => await _context.{Entity}s
            .AsNoTracking()
            .FirstOrDefaultAsync(e => e.Id == id, ct);

    public async Task<List<{Entity}>> GetAllAsync(CancellationToken ct)
        => await _context.{Entity}s
            .AsNoTracking()
            .ToListAsync(ct);

    public async Task AddAsync({Entity} entity, CancellationToken ct)
    {
        await _context.{Entity}s.AddAsync(entity, ct);
        await _context.SaveChangesAsync(ct);
    }

    public async Task UpdateAsync({Entity} entity, CancellationToken ct)
    {
        _context.{Entity}s.Update(entity);
        await _context.SaveChangesAsync(ct);
    }

    public async Task DeleteAsync({Entity} entity, CancellationToken ct)
    {
        _context.{Entity}s.Remove(entity);
        await _context.SaveChangesAsync(ct);
    }
}
```

---

## Dependency Injection Pattern

Entry orchestrator calls private sub-methods only - no direct registrations.
Each sub-method owns exactly one technical concern.
Location: `Infrastructure/DependencyInjection.cs`

### Entry Method

```csharp
/// <summary>
/// Entry orchestrator - only calls sub-methods, no direct registrations.
/// Add a new private sub-method when introducing a new technical concern.
/// </summary>
public static IServiceCollection AddInfrastructureServices(
    this IServiceCollection services,
    string connectionString)
{
    AddDatabase(services, connectionString);
    AddRepositories(services);
    AddCoreServices(services);
    AddHttpClients(services);


    AddCaching(services);
    AddAuthentication(services, configuration);
    AddBlobStorage(services, configuration);

    return services;
}
```

---

### AddDatabase

Registers `ApplicationDbContext` and its interface.
Accepts the connection string directly - passed from the WebApi configuration layer.

```csharp
private static void AddDatabase(IServiceCollection services, string connectionString)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseNpgsql(connectionString));

    services.AddScoped<IApplicationDbContext>(sp =>
        sp.GetRequiredService<ApplicationDbContext>());
}
```

---

### AddRepositories

Registers all `IXxxRepository` to `XxxRepository` mappings.
Add every new repository pair here - one line per repository.

```csharp
private static void AddRepositories(IServiceCollection services)
{
    services.AddScoped<IDocumentRepository, DocumentRepository>();
    ....
}
```

---

### AddCoreServices

Registers user context, notification services, and other infrastructure-level services.

```csharp
private static void AddCoreServices(IServiceCollection services)
{
    services.AddHttpContextAccessor();
    services.AddScoped<IUserContext, UserContext>();
    services.AddScoped<IDocumentExpiryNotificationService, DocumentExpiryNotificationService>();
}
```

---

### AddHttpClients

Registers all typed `HttpClient` instances.
Configure timeouts and base addresses here - never inline in service constructors.

```csharp
private static void AddHttpClients(IServiceCollection services)
{
    services.AddHttpClient<ILinkCheckService, LinkCheckService>(client =>
    {
        client.Timeout = TimeSpan.FromSeconds(10);
    });

    // Example: Refit-based typed client with DelegatingHandler
    services.AddHttpClient<IExternalApiClient, ExternalApiClient>(client =>
    {
        client.BaseAddress = new Uri("https://api.example.com");
        client.Timeout = TimeSpan.FromSeconds(30);
    })
    .AddHttpMessageHandler<AuthHeaderHandler>();
}
```

---

### AddCaching

Configures in-memory cache with size limits to prevent unbounded growth.
Add when any service requires `IMemoryCache` or `ICacheService`.

```csharp
private static void AddCaching(IServiceCollection services)
{
    services.AddMemoryCache(options =>
    {
        // Limit to 1000 entries maximum
        options.SizeLimit = 1000;

        // When limit is reached, compact by removing 25% of entries
        options.CompactionPercentage = 0.25;

        // Scan for expired items every 5 minutes
        options.ExpirationScanFrequency = TimeSpan.FromMinutes(5);
    });

    services.AddSingleton<ICacheService, MemoryCacheService>();
}
```

---

### AddAuthentication

Configures JWT Bearer authentication from the `Authentication` config section.
Silently falls back to authorization-only when `Authority` or `Audience` is not configured.

```csharp
 // Authentication
    private static void AddAuthentication(
        IServiceCollection services,
        IConfiguration configuration)
    {
        services.Configure<IdentityOptions>(
           configuration.GetSection(IdentityOptions.SectionName));
        var identityConfig = configuration.GetSection(IdentityOptions.SectionName).Get<IdentityOptions>() ?? throw new InvalidOperationException("IdentityOptions configuration is missing.");

        if (string.IsNullOrWhiteSpace(identityConfig.Authority) || string.IsNullOrWhiteSpace(identityConfig.Audience))
        {
            // Identity not configured - register authorization only and skip silently
            services.AddAuthorization();
            return;
        }

        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Authority = identityConfig.Authority;
                options.Audience = identityConfig.Audience;
                options.IncludeErrorDetails = true;
                options.RequireHttpsMetadata = true;

                options.TokenValidationParameters = new TokenValidationParameters
                {
                    ValidIssuer = identityConfig.Authority,
                    ValidAudience = identityConfig.Audience,
                    ValidateIssuer = true,
                    ValidateAudience = true,
                    ValidateLifetime = true,
                    ValidateIssuerSigningKey = true
                };

                options.Events = new JwtBearerEvents
                {
                    OnAuthenticationFailed = context =>
                    {
                        context.Response.StatusCode = StatusCodes.Status401Unauthorized;
                        context.Response.ContentType = "application/json";
                        return context.Response.WriteAsync(
                            JsonSerializer.Serialize(new { error = "Authentication failed." }));
                    }
                };
            });

        services.AddAuthorization();
    }
```

---

### AddBlobStorage

Registers Azure Blob Storage services.
Optional - silently skips registration when the connection string is absent.

```csharp
private static void AddBlobStorage(
    IServiceCollection services,
    IConfiguration configuration)
{
    var connectionString = configuration.GetConnectionString("AzureStorage");

    if (string.IsNullOrWhiteSpace(connectionString))
    {
        // Optional integration - skip silently when not configured
        return;
    }

    services.Configure<AzureStorageOptions>(
        configuration.GetSection(AzureStorageOptions.SectionName));

    services.AddSingleton(_ => new BlobServiceClient(connectionString));
    services.AddScoped<IBlobStorageService, AzureBlobStorageService>();
}
```
