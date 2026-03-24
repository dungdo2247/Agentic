# API Patterns

> These patterns cover common API endpoint implementations.
> Use the variant that matches your project's architecture.
> During Tier 2 setup, irrelevant variants are removed and project-specific patterns are generated.

---

## Variant: Minimal API + MediatR (CQRS)

Route path lowercase. `WithTags` PascalCase. Use `TypedResults`. Declare `Produces` on every endpoint.

```csharp
using MediatR;
using Microsoft.AspNetCore.Http.HttpResults;

public static class {DomainModel}Endpoints
{
    public static void Map{DomainModel}Endpoints(this IEndpointRouteBuilder app)
    {
        var group = app.MapGroup("/api/v1/{domainmodel}").WithTags("{DomainModel}");

        group.MapGet("/{id:guid}", async (Guid id, ISender sender, CancellationToken ct) =>
        {
            var result = await sender.Send(new Get{DomainModel}Query(id), ct);
            return result is not null ? TypedResults.Ok(result) : TypedResults.NotFound();
        })
        .WithName("Get{DomainModel}ById")
        .Produces<{DomainModel}Dto>(StatusCodes.Status200OK)
        .ProducesProblem(StatusCodes.Status404NotFound);
    }
}
```

---

## Variant: Minimal API + Service Layer

```csharp
using Microsoft.AspNetCore.Http.HttpResults;

public static class {DomainModel}Endpoints
{
    public static void Map{DomainModel}Endpoints(this IEndpointRouteBuilder app)
    {
        var group = app.MapGroup("/api/v1/{domainmodel}").WithTags("{DomainModel}");

        group.MapGet("/{id:guid}", async (Guid id, I{DomainModel}Service service, CancellationToken ct) =>
        {
            var result = await service.GetByIdAsync(id, ct);
            return result is not null ? TypedResults.Ok(result) : TypedResults.NotFound();
        })
        .WithName("Get{DomainModel}ById")
        .Produces<{DomainModel}Dto>(StatusCodes.Status200OK)
        .ProducesProblem(StatusCodes.Status404NotFound);
    }
}
```

---

## Variant: MVC Controller + Service Layer

```csharp
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/v1/[controller]")]
public class {DomainModel}Controller : ControllerBase
{
    private readonly I{DomainModel}Service _service;

    public {DomainModel}Controller(I{DomainModel}Service service)
        => _service = service;

    [HttpGet("{id:guid}")]
    [ProducesResponseType(typeof({DomainModel}Dto), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<IActionResult> GetById(Guid id, CancellationToken ct)
    {
        var result = await _service.GetByIdAsync(id, ct);
        return result is not null ? Ok(result) : NotFound();
    }

    [HttpPost]
    [ProducesResponseType(typeof(Guid), StatusCodes.Status201Created)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> Create([FromBody] Create{DomainModel}Request request, CancellationToken ct)
    {
        var id = await _service.CreateAsync(request, ct);
        return CreatedAtAction(nameof(GetById), new { id }, id);
    }
}
```

---

## Variant: MVC Controller + MediatR (CQRS)

```csharp
using MediatR;
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/v1/[controller]")]
public class {DomainModel}Controller : ControllerBase
{
    private readonly ISender _sender;

    public {DomainModel}Controller(ISender sender)
        => _sender = sender;

    [HttpGet("{id:guid}")]
    [ProducesResponseType(typeof({DomainModel}Dto), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<IActionResult> GetById(Guid id, CancellationToken ct)
    {
        var result = await _sender.Send(new Get{DomainModel}Query(id), ct);
        return result is not null ? Ok(result) : NotFound();
    }
}
```

---

## OpenAPI / Scalar Setup

File: `{ProjectName}.WebApi/Extensions/OpenApiExtensions.cs`
Must be registered **after** `UseAuthentication()` / `UseAuthorization()`.

```csharp
// Program.cs
builder.Services.AddOpenApiServices();

app.UseAuthentication();
app.UseAuthorization();
app.UseOpenApiPipeline();
```

```csharp
public static class OpenApiExtensions
{
    public static IServiceCollection AddOpenApiServices(this IServiceCollection services)
    {
        services.AddOpenApi(options =>
        {
            options.AddOperationTransformer<AddApiHeaderParameter>();

            options.AddDocumentTransformer((document, context, cancellationToken) =>
            {
                document.Servers =
                [
                    new() { Url = "{ProjectRouteInCustomDNS}", Description = "Production" },
                    new() { Url = "/", Description = "Root" }
                ];
                document.Info = new()
                {
                    Title = "",
                    Version = "v1",
                    Description = "API for "
                };
                return Task.CompletedTask;
            });
        });
        return services;
    }

    public static WebApplication UseOpenApiPipeline(this WebApplication app)
    {
        app.MapOpenApi();
        app.MapScalarApiReference();
        return app;
    }
}
```

---

## API Key Authentication Middleware

Validates `X-Api-Key` header against `ApplicationOptions.ApiKey`.
Location: `WebApi/Middleware/ApiKeyAuthenticationMiddleware.cs`

```csharp
public class ApiKeyAuthenticationMiddleware
{
    private const string ApiKeyHeaderName = "X-Api-Key";
    private readonly RequestDelegate _next;

    public ApiKeyAuthenticationMiddleware(RequestDelegate next) => _next = next;

    public async Task InvokeAsync(HttpContext context, IOptions<ApplicationOptions> options)
    {
        if (!context.Request.Headers.TryGetValue(ApiKeyHeaderName, out var providedKey)
            || !string.Equals(providedKey, options.Value.ApiKey, StringComparison.Ordinal))
        {
            context.Response.StatusCode = StatusCodes.Status401Unauthorized;
            await context.Response.WriteAsync("Unauthorized");
            return;
        }

        await _next(context);
    }
}
```

### Registration order in Program.cs

```csharp
app.UseMiddleware<ExceptionHandlingMiddleware>();
app.UseMiddleware<ApiKeyAuthenticationMiddleware>(); // before UseAuthorization
app.UseAuthorization();
app.UseOpenApiPipeline();                            // after UseAuthorization
```

---

## AddApiHeaderParameter — OpenAPI Operation Transformer

Injects the `X-Api-Key` required header into every OpenAPI operation so Scalar shows it.
Location: `WebApi/Extensions/AddApiHeaderParameter.cs`

```csharp
using Microsoft.AspNetCore.OpenApi;
using Microsoft.OpenApi.Models;

public class AddApiHeaderParameter : IOpenApiOperationTransformer
{
    public Task TransformAsync(
        OpenApiOperation operation,
        OpenApiOperationTransformerContext context,
        CancellationToken cancellationToken)
    {
        operation.Parameters ??= [];
        operation.Parameters.Add(new OpenApiParameter
        {
            Name = "X-Api-Key",
            In = ParameterLocation.Header,
            Required = true,
            Schema = new OpenApiSchema { Type = "string" }
        });
        return Task.CompletedTask;
    }
}
```

Register in `OpenApiExtensions.cs`:

```csharp
services.AddOpenApi(options =>
{
    options.AddOperationTransformer<AddApiHeaderParameter>();
    // ... document transformer
});
```
