---
name: Domain Layer Instructions
description: Constraints for domain models, value objects, exceptions, and layer purity in the Domain layer.
applyTo: "**/*.Domain/**"
---

# Domain Layer Instructions

## Related Pattern Files

- `patterns/CodePatterns.md`

## Folder Structure

| Folder | Purpose |
|---|---|
| `Common/` | Shared domain primitives (e.g. `Result<T>`, `Error`) |
| `Exceptions/` | Domain-specific exception types and error codes |
| `Models/` | Business value objects and domain models |
| `Utils/` | Domain utility helpers (pure functions only) |

## Domain Model Rules

### Allowed

- Business models and value objects
- Domain enums
- Domain exceptions and error codes
- Pure utility helpers with no external dependencies

### Forbidden

- Request DTOs
- Response DTOs
- API query models
- EF Core entities or any persistence annotations (`[Table]`, `[Column]`, etc.)
- Persistence-specific logic (`DbContext`, migrations, queries)
- External service response models
- References to Application, Infrastructure, or WebApi namespaces
- Third-party NuGet packages from outer layers
