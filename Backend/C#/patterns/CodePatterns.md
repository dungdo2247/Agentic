# General Code Patterns

> Background job patterns vary by job runner (Hangfire, Quartz, hosted services, etc.).
> Use the variant that matches your project. Irrelevant variants are removed during Tier 2 setup.

---

## Variant: Hangfire Background Job

`IJobCancellationToken` for graceful shutdown. `[AutomaticRetry]` for resilience.
Location: `Infrastructure/Services/HangfireJobService/`

```csharp
using Hangfire;

public class {Purpose}Job
{
    private readonly ILogger<{Purpose}Job> _logger;

    public {Purpose}Job(ILogger<{Purpose}Job> logger) => _logger = logger;

    [AutomaticRetry(Attempts = 3)]
    public async Task ExecuteAsync(Guid entityId, IJobCancellationToken cancellationToken)
    {
        cancellationToken.ThrowIfCancellationRequested();
        _logger.LogInformation("Processing {EntityId}", entityId);

        // Implementation logic
    }
}
```

---

## Result\<T\> Domain Primitive

Avoid throwing exceptions for expected business failures.
Location: `Domain/Common/`

```csharp
public class Result<T>
{
    public bool IsSuccess { get; }
    public T? Value { get; }
    public string? Error { get; }

    private Result(T value)      { IsSuccess = true;  Value = value; }
    private Result(string error) { IsSuccess = false; Error = error; }

    public static Result<T> Success(T value)      => new(value);
    public static Result<T> Failure(string error) => new(error);
}
```

---

## Hangfire Recurring Job with Scoped Services

Use `IServiceScopeFactory` when the job needs EF Core repositories or any scoped dependency.
Location: `Infrastructure/Services/HangfireJobService/`

```csharp
using Hangfire;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class {Purpose}Job
{
    private readonly IServiceScopeFactory _scopeFactory;
    private readonly ILogger<{Purpose}Job> _logger;

    public {Purpose}Job(IServiceScopeFactory scopeFactory, ILogger<{Purpose}Job> logger)
    {
        _scopeFactory = scopeFactory;
        _logger = logger;
    }

    [AutomaticRetry(Attempts = 3)]
    public async Task ExecuteAsync(IJobCancellationToken cancellationToken)
    {
        cancellationToken.ThrowIfCancellationRequested();

        await using var scope = _scopeFactory.CreateAsyncScope();
        var repository = scope.ServiceProvider.GetRequiredService<I{Entity}Repository>();

        // implementation logic using repository
        _logger.LogInformation("{Purpose}Job completed", nameof({Purpose}Job));
    }
}
```

---

## Recurring Job Registration in Program.cs

Register all recurring jobs after `app.Build()` using `IRecurringJobManager`.

```csharp
var jobs = app.Services.GetRequiredService<IRecurringJobManager>();

jobs.AddOrUpdate<CheckDocumentLinksJob>(
    "check-document-links",
    job => job.ExecuteAsync(JobCancellationToken.Null),
    Cron.Daily);

jobs.AddOrUpdate<CheckDocumentExpiryJob>(
    "check-document-expiry",
    job => job.ExecuteAsync(JobCancellationToken.Null),
    Cron.Daily);
```

