---
name: Infrastructure Layer Instructions
description: Implementation rules for persistence, adapters, caching, background jobs, and dependency injection in Infrastructure.
applyTo: "**/*.Infrastructure/**"
---

# Infrastructure Layer Instructions

## Purpose

This file defines rules for the Infrastructure layer.
It is the source of policy for persistence, external integrations, caching, jobs, and DI organization.

## Related Pattern Files

- `patterns/InfrastructurePatterns.md`
- `patterns/EntityFrameworkCorePatterns.md`
- `patterns/CodePatterns.md`
- `patterns/LogPatterns.md`

---

### Folder Structure

| Folder | Purpose |
|---|---|
| `Caches/` | Caching helpers and decorated cache services |
| `Files/` | File handling utilities (e.g. blob storage services) |
| `Services/` | Implementations of Application interfaces, external adapters |
| `Services/{JobRunner}Jobs/` | Background job implementations |
| `Persistence/` | DbContext, database factory |
| `Persistence/Configurations/` | ORM entity/type configuration classes |
| `Persistence/Repositories/` | Repository implementations |
| `Migrations/` | ORM-generated migration files |

> For the exact folder structure of your project, see `Infrastructure.Project.Instructions.md`.

---

## Implementation Rules

- Implement all interfaces defined in Application
- Never expose DbContext directly to Application — always wrap in a repository or unit-of-work
- Use read-only queries by default for read operations (e.g. `.AsNoTracking()` in EF Core)
- Use ORM configuration classes for entity mappings — never configure in entity classes
- Use `DelegatingHandler` for injecting auth headers into HTTP clients
- Use cache-aside pattern (get-or-create) for cache operations to prevent stampedes
- Register all services via `IServiceCollection` extension methods
- Keep observability/telemetry implementations under a dedicated folder in Infrastructure

---

## Dependency Injection

- `DependencyInjection.cs` has a single public entry method `AddInfrastructureServices` that only orchestrates calls to private sub-methods. No direct registrations in the entry method.
- Each private sub-method registers one technical concern only.
- Sub-methods are `private static void` — never exposed outside `DependencyInjection.cs`.
- Naming convention: `Add{TechnicalConcern}` (e.g. `AddDatabase`, `AddRepositories`, `AddCoreServices`, `AddHttpClients`, `AddAuthentication`, `AddCaching`).
- Add a new private sub-method for each new technical concern — never mix concerns inside an existing sub-method.
- Optional integrations must silently skip registration when their configuration is absent — never throw on missing optional config.
- See `InfrastructurePatterns.md` for fully implemented reference sub-methods.

> For the current sub-methods table of your project, see `Infrastructure.Project.Instructions.md`.

### Adding a new concern checklist

1. Create a `private static void Add{Concern}(...)` method in `DependencyInjection.cs`
2. Add the call to `AddInfrastructureServices`
3. If the concern is optional, guard with a null/empty config check and return silently
4. Update the sub-methods table in `Infrastructure.Project.Instructions.md`

---

## Observability

- Keep telemetry and request logging implementation in Infrastructure.
- Register observability via a dedicated DI concern method, e.g. `AddObservability(...)`.
- WebApi should only add middleware to the request pipeline; implementation stays in Infrastructure.
- See `patterns/LogPatterns.md` for reference setup.

---

## External Service Adapters

- Wrap all external HTTP APIs behind an Application interface
- Map HTTP errors to domain exceptions — never propagate raw `HttpRequestException`

---

## Background Jobs

- Place under a dedicated folder in `Services/`
- Accept cancellation tokens for graceful shutdown
- Configure automatic retry with sensible defaults
- Use scoped service resolution when jobs need scoped dependencies like DbContext

---

## Audit Fields

- `CreatedBy` and `CreatedAt` are set once on creation and must not be overwritten on subsequent updates.
- `UpdatedBy` and `UpdatedAt` are set on every modification and reflect the identity of the last editor.

---

## Authentication and Authorization

- The Infrastructure layer provides implementations for user context abstractions (e.g. `IUserContext`).
- Expose information from the current authenticated user context (user ID, roles, claims) to support authorization decisions in the Application layer.
- Create extension methods for common user context transformations to avoid spreading authentication details across the codebase.
- Create UserContextExtensions for common transformations (e.g. GetUserId()) to avoid spreading authentication details across the codebase.