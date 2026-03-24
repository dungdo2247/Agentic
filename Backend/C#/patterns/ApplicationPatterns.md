# Application Patterns

## MediatR Query + Handler

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

---

## FluentValidation Validator

Auto-discovered by `ValidationBehavior` — do NOT invoke manually.
File placed co-located with its command/query as `{Name}Validator.cs`.

```csharp
using FluentValidation;

namespace {ProjectName}.Application.Features.{Feature}.Commands;

public class {CommandName}Validator : AbstractValidator<{CommandName}>
{
    public {CommandName}Validator()
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

        RuleFor(x => x.TenantId)
            .NotEmpty().WithMessage("Tenant ID is required");
    }
}
```

---

## ValidationBehavior (MediatR Pipeline)

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
