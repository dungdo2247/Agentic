# Application Insights and Request Logging Patterns

## Layer Placement

- **Infrastructure** - telemetry implementation under `Infrastructure/ApplicationInsights/`.
- **WebApi** - only wires services and middleware into the pipeline.

## NuGet (Infrastructure)

- `Microsoft.ApplicationInsights.AspNetCore`
- `Newtonsoft.Json`

## Infrastructure DI Registration

Register in a dedicated `AddObservability` method inside `Infrastructure/DependencyInjection.cs`:

```csharp
private static void AddObservability(IServiceCollection services, IConfiguration configuration)
{
    services.AddApplicationInsightsTelemetry(configuration);
    services.AddTransient<RequestBodyLoggingMiddleware>();
    services.AddTransient<ApiTrackingEndpointFilter>();
}
```

## WebApi Pipeline Order

```csharp
app.UseRouting();
app.UseMiddleware<RequestBodyLoggingMiddleware>();
app.UseAuthentication();
app.UseAuthorization();
```

Request body middleware runs **before** auth so telemetry captures bodies consistently.

## Endpoint Tracking (Minimal API)

Attach tracking to each endpoint via the `WithApiTracking` extension:

```csharp
group.MapPost("/", handler)
    .WithName("CreateOrder")
    .WithApiTracking("CreateOrder", "MyEpic", "Create")
    .Produces<Guid>(201);
```

`WithApiTracking` adds `ApiTrackingActionFilter` metadata **and** `ApiTrackingEndpointFilter` in one call.

## Key Classes

### `ApiTrackingActionFilter` - Endpoint metadata marker

```csharp
namespace {ProjectName}.Infrastructure.ApplicationInsights;

[AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = true)]
public sealed class ApiTrackingActionFilter : Attribute
{
    public ApiTrackingActionFilter(string funcName, string epicName, string feature) { ... }
    public string FuncName { get; }
    public string EpicName { get; }
    public string Feature { get; }   // Defaults to "Search" when empty
}
```

### `ApiTrackingEndpointFilter` - Telemetry enrichment (`IEndpointFilter`)

Wraps endpoint execution, measures duration, and writes all tracked properties to `RequestTelemetry`. Fires `TelemetryClient.TrackEvent` after the response.

### `RequestBodyLoggingMiddleware` - Request body capture (`IMiddleware`)

Reads POST/PUT bodies into `RequestTelemetry.Properties["RequestBody"]` **only when** the endpoint has `ApiTrackingActionFilter` metadata.

```csharp
if (context.GetEndpoint()?.Metadata.GetMetadata<ApiTrackingActionFilter>() is not null)
{
    requestTelemetry?.Properties.TryAdd("RequestBody", requestBody);
}
```

### `ApiTrackingExtensions` - Fluent wiring helper

```csharp
public static RouteHandlerBuilder WithApiTracking(
    this RouteHandlerBuilder builder,
    string funcName, string epicName, string feature)
{
    return builder
        .WithMetadata(new ApiTrackingActionFilter(funcName, epicName, feature))
        .AddEndpointFilter<ApiTrackingEndpointFilter>();
}
```

## Telemetry Properties Reference

> Note: claim keys are project-specific. Prefer mapping from your auth system into a small set of normalized telemetry fields.

| Property        | Source             | Description                                         |
| --------------- | ------------------ | --------------------------------------------------- |
| `Function`      | `funcName` param   | Endpoint/operation name                             |
| `Epic`          | `epicName` param   | Business epic for grouping                          |
| `Feature`       | `feature` param    | Feature category (Search, Create, Update, Delete)   |
| `AreaPath`      | Config or constant | Optional grouping field for dashboards              |
| `IsSuccess`     | Try/catch result   | True or False                                       |
| `Duration`      | Stopwatch ms       | Endpoint execution time                             |
| `Date`          | `DateTime.UtcNow`  | UTC timestamp (ISO 8601)                            |
| `RequestBody`   | Middleware         | Serialized POST/PUT body                            |
| `ResponseBody`  | Filter             | Serialized response object                          |
| `UserIpAddress` | `RemoteIpAddress`  | Client IP                                           |
| `UserName`      | Claim              | Display name (optional)                             |
| `UserEmail`     | Claim              | Email address (optional)                            |
| `TenantId`      | Claim or header    | Tenant/organisation identifier (if multi-tenant)    |
| `TenantName`    | Claim or lookup    | Tenant/organisation display name (optional)         |
| `UserRole`      | Claim              | Role or user type (optional)                        |
| `UserIdHash`    | Claim              | Hashed user identifier (avoid raw IDs if sensitive) |

## appsettings.json

```json
{
  "ApplicationInsights": {
    "ConnectionString": "InstrumentationKey=...;IngestionEndpoint=https://..."
  }
}
```
