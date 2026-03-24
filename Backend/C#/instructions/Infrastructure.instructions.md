---
name: Infrastructure Layer Instructions
description: Implementation rules for persistence, adapters, caching, Hangfire jobs, and dependency injection in Infrastructure.
applyTo: "**/*.Infrastructure/**"
---

## Related Pattern Files

- `patterns/InfrastructurePatterns.md`
- `patterns/EntityFrameworkCorePatterns.md`
- `patterns/CodePatterns.md`
- `patterns/LogPatterns.md`

## Layer Breakdown

### Source Projects

| Project | Role |
| --- | --- |
| Domain | Core business models, enums, exceptions, and utilities. No dependencies on other layers. |
| Application | Use-cases CQRS commands/queries (MediatR), DTOs, pipeline behaviors, interfaces. Depends only on Domain. |
| Infrastructure | Technical implementations caching, external service adapters, observability/telemetry. Implements interfaces defined in Application. |
| WebApi | Entry point Minimal API endpoints, middleware, DI bootstrapping. Depends on Application and Infrastructure. |

---

### ProjectName.Infrastructure Folder Structure

| Folder | Purpose |
|---|---|
| Caches/ | Caching helpers and decorated cache services |
| Files/ | File handling utilities (e.g. AzureBlobStorageService) |
| Services/ | Implementations of Application interfaces, external adapters |
| Services/HangfireJobService/ | Background job implementations |
| Persistence/ | ApplicationDbContext, ApplicationDbContextFactory |
| Persistence/Configurations/ | EF Core IEntityTypeConfiguration classes |
| Persistence/Repositories/ | Repository implementations |
| Migrations/ | EF Core generated migration files |
| Migrations/DBScripts/ | Idempotent SQL scripts generated from migrations |

---

## Implementation Rules

- Implement all interfaces defined in Application/Common/Interfaces/...
- Never expose DbContext to Application - always wrap in a repository
- Use .AsNoTracking() by default on all read queries
- Use IEntityTypeConfiguration for EF Core mappings - never configure in entity classes
- Use DelegatingHandler for injecting auth headers into HTTP clients
- Use GetOrCreateAsync for cache operations to prevent stampedes
- Register all services via IServiceCollection extension methods in WebApi/Extensions/
- Place Application Insights middleware/telemetry implementations under `Infrastructure/ApplicationInsights/`

---

## Dependency Injection

- DependencyInjection.cs has a single public entry method AddInfrastructureServices that only orchestrates calls to private sub-methods. No direct registrations in the entry method.
- Each private sub-method registers one technical concern only.
- Sub-methods are private static void - never exposed outside DependencyInjection.cs.
- Naming convention: Add{TechnicalConcern} e.g. AddDatabase, AddRepositories, AddCoreServices, AddHttpClients, AddAuthentication, AddCaching.
- Add a new private sub-method for each new technical concern - never mix concerns inside an existing sub-method.
- Optional integrations must silently skip registration when their configuration is absent - never throw on missing optional config.
- See InfrastructurePatterns.md for fully implemented reference sub-methods.

### Current sub-methods

| Method | Concern |
|---|---|
| AddDatabase(services, connectionString) | ApplicationDbContext and IApplicationDbContext |
| AddRepositories(services) | All IXxxRepository to XxxRepository |
| AddCoreServices(services) | IUserContext, IDocumentExpiryNotificationService, HttpContextAccessor |
| AddHttpClients(services) | Typed HttpClient registrations |
| AddObservability(services, configuration) | Application Insights telemetry + logging middleware registrations |

### Adding a new concern checklist

1. Create a private static void Add{Concern}(...) method in DependencyInjection.cs
2. Add the call to AddInfrastructureServices
3. If the concern is optional, guard with a null/empty config check and return silently
4. Update the sub-methods table above

---

## Observability (Application Insights)

- Keep telemetry and request logging implementation in Infrastructure.
- Register observability via a dedicated DI concern method, e.g. `AddObservability(...)`.
- WebApi should only add middleware to the request pipeline; implementation stays in Infrastructure.
- See `patterns/LogPatterns.md` for reference setup.

---

## External Service Adapters

- Wrap all external HTTP APIs behind an Application interface
- Use Refit for typed HTTP clients
- Map HTTP errors to domain exceptions - never propagate raw HttpRequestException

---

## Background Jobs (Hangfire)

- Place under Services/HangfireJobService/
- Accept IJobCancellationToken for graceful shutdown
- Decorate with [AutomaticRetry(Attempts = 3)]

---

## Audit Fields

- CreatedBy and CreatedAt are set once on creation and must not be overwritten on subsequent updates.
- UpdatedBy and UpdatedAt are set on every modification and reflect the identity of the last editor.

---

## Authentication and Authorization

- The Infrastructure layer provides implementations for IUserContext.
- Expose information from the current authenticated user context (user ID, roles) to support authorization decisions in the Application layer.
- Create UserContextExtensions for common transformations (e.g. GetUserId()) to avoid spreading authentication details across the codebase.