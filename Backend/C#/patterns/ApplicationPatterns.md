# Application Patterns

> These patterns cover common Application layer implementations.
> Use the variant that matches your project's architecture.
> During Tier 2 setup, irrelevant variants are removed and project-specific patterns are generated.

---

## Variant: CQRS + MediatR

### Query + Handler

```csharp
namespace {ProjectName}.Application.Features.{Feature}.Queries.{QueryName};

public record {QueryName}Query(Guid Id) : IRequest<{QueryName}Dto?>;

public class {QueryName}QueryHandler : IRequestHandler<{QueryName}Query, {QueryName}Dto?>
{
    private readonly I{Entity}Repository _repository;

    public {QueryName}QueryHandler(I{Entity}Repository repository)
        => _repository = repository;

    public async Task<{QueryName}Dto?> Handle({QueryName}Query request, CancellationToken ct)
    {
        var entity = await _repository.GetByIdAsync(request.Id, ct);
        return entity is null ? null : new {QueryName}Dto(/* map */);
    }
}
```

### Command + Handler

```csharp
namespace {ProjectName}.Application.Features.{Feature}.Commands.{CommandName};

public record {CommandName}Command(/* properties */) : IRequest<Guid>;

public class {CommandName}CommandHandler : IRequestHandler<{CommandName}Command, Guid>
{
    private readonly I{Entity}Repository _repository;

    public {CommandName}CommandHandler(I{Entity}Repository repository)
        => _repository = repository;

    public async Task<Guid> Handle({CommandName}Command request, CancellationToken ct)
    {
        var entity = new {Entity}(/* map from request */);
        await _repository.AddAsync(entity, ct);
        return entity.Id;
    }
}
```

### ValidationBehavior (MediatR Pipeline)

File: `Application/Common/Behaviors/ValidationBehavior.cs` — registered once globally.

```csharp
using FluentValidation;
using MediatR;

public class ValidationBehavior<TRequest, TResponse> : IPipelineBehavior<TRequest, TResponse>
    where TRequest : notnull
{
    private readonly IEnumerable<IValidator<TRequest>> _validators;

    public ValidationBehavior(IEnumerable<IValidator<TRequest>> validators)
        => _validators = validators;

    public async Task<TResponse> Handle(
        TRequest request, RequestHandlerDelegate<TResponse> next, CancellationToken ct)
    {
        if (!_validators.Any()) return await next();

        var context = new ValidationContext<TRequest>(request);
        var failures = _validators
            .Select(v => v.Validate(context))
            .SelectMany(r => r.Errors)
            .Where(f => f is not null)
            .ToList();

        if (failures.Count != 0) throw new ValidationException(failures);

        return await next();
    }
}
```

---

## Variant: Service Layer

### Service Interface + Implementation

```csharp
namespace {ProjectName}.Application.Interfaces;

public interface I{Entity}Service
{
    Task<{Entity}Dto?> GetByIdAsync(Guid id, CancellationToken ct);
    Task<List<{Entity}Dto>> GetAllAsync(CancellationToken ct);
    Task<Guid> CreateAsync(Create{Entity}Request request, CancellationToken ct);
    Task UpdateAsync(Guid id, Update{Entity}Request request, CancellationToken ct);
    Task DeleteAsync(Guid id, CancellationToken ct);
}
```

```csharp
namespace {ProjectName}.Application.Services;

public class {Entity}Service : I{Entity}Service
{
    private readonly I{Entity}Repository _repository;
    private readonly ILogger<{Entity}Service> _logger;

    public {Entity}Service(I{Entity}Repository repository, ILogger<{Entity}Service> logger)
    {
        _repository = repository;
        _logger = logger;
    }

    public async Task<{Entity}Dto?> GetByIdAsync(Guid id, CancellationToken ct)
    {
        var entity = await _repository.GetByIdAsync(id, ct);
        return entity is null ? null : MapToDto(entity);
    }

    public async Task<Guid> CreateAsync(Create{Entity}Request request, CancellationToken ct)
    {
        var entity = new {Entity}(/* map from request */);
        await _repository.AddAsync(entity, ct);
        return entity.Id;
    }

    private static {Entity}Dto MapToDto({Entity} entity) => new(/* map */);
}
```

---

## FluentValidation Validator (all patterns)

File placed co-located with its use-case as `{Name}Validator.cs`.

```csharp
using FluentValidation;

namespace {ProjectName}.Application.Features.{Feature};

public class {Name}Validator : AbstractValidator<{Name}>
{
    public {Name}Validator()
    {
        RuleFor(x => x.Id)
            .NotEmpty().WithMessage("ID is required");

        RuleFor(x => x.Email)
            .NotEmpty().WithMessage("Email is required")
            .EmailAddress().WithMessage("A valid email address is required");

        RuleFor(x => x.Title)
            .NotEmpty().WithMessage("Title is required")
            .MinimumLength(3).WithMessage("Title must be at least 3 characters")
            .MaximumLength(200).WithMessage("Title must not exceed 200 characters");

        RuleFor(x => x.Description)
            .MaximumLength(200)
            .When(x => !string.IsNullOrWhiteSpace(x.Description));

        RuleFor(x => x.Status)
            .IsInEnum().WithMessage("Invalid status value");
    }
}
```
```
