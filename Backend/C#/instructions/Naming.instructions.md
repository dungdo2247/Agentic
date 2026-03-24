---
name: Naming Conventions Instructions
description: Standard naming patterns for projects, artifacts, endpoints, files, and C# identifiers.
applyTo: "**"
---

# Naming Conventions

## Related Pattern Files

- `patterns/StructurePatterns.md`
- `patterns/ApplicationPatterns.md`
- `patterns/ApiPatterns.md`
- `patterns/InfrastructurePatterns.md`
- `patterns/LogPatterns.md`
- `patterns/TestingPatterns.md`

## Project & Namespace Suffixes

| Layer | Suffix | Example |
|---|---|---|
| Domain | `.Domain` | `MyProject.Domain` |
| Application | `.Application` | `MyProject.Application` |
| Infrastructure | `.Infrastructure` | `MyProject.Infrastructure` |
| WebApi | `.WebApi` | `MyProject.WebApi` |
| Domain Tests | `.Domain.Tests` | `MyProject.Domain.Tests` |
| Application Tests | `.Application.Tests` | `MyProject.Application.Tests` |
| Integration Tests | `.IntegrationTests` | `MyProject.IntegrationTests` |

## Application Artifacts

Naming depends on the architecture pattern used. Below are conventions for common patterns.

### CQRS Pattern

| Type | Pattern | Example |
|---|---|---|
| Query | `{Name}Query` | `Get{Entity}Query` |
| Query Handler | `{Name}QueryHandler` | `Get{Entity}QueryHandler` |
| Command | `{Name}Command` | `Create{Entity}Command` |
| Command Handler | `{Name}CommandHandler` | `Create{Entity}CommandHandler` |
| Validator | `{Name}Validator` | `Create{Entity}CommandValidator` |

### Service Layer Pattern

| Type | Pattern | Example |
|---|---|---|
| Service interface | `I{Entity}Service` | `IOrderService` |
| Service class | `{Entity}Service` | `OrderService` |
| Validator | `{Name}Validator` | `CreateOrderValidator` |

> For your project's specific naming, see `.Project.Instructions.md` files.

## DTOs

| Type | Pattern | Example |
|---|---|---|
| Response DTO | `{Name}Dto` | `{Entity}Dto` |

## API Endpoints / Controllers

| Convention | Rule | Example |
|---|---|---|
| Route path | lowercase | `/api/v1/{resource}` |
| Endpoint class (Minimal API) | `{DomainModel}Endpoints` | `OrderEndpoints` |
| Controller class (MVC) | `{DomainModel}Controller` | `OrderController` |

## File Naming

| Type | Pattern | Example |
|---|---|---|
| Validator | `{Name}Validator.cs` | `CreateOrderValidator.cs` |
| ORM config | `{Entity}Configuration.cs` | `OrderConfiguration.cs` |
| Repository | `{Entity}Repository.cs` | `OrderRepository.cs` |
| Interface | `I{Name}.cs` | `IOrderRepository.cs` |
| Extension class | `{Purpose}Extensions.cs` | `OpenApiExtensions.cs` |
| Cache service | `{Entity}CacheService.cs` | `OrderCacheService.cs` |
| Background job | `{Purpose}Job.cs` | `ProcessDocumentJob.cs` |

## General C# Rules

- Classes, methods, properties: `PascalCase`
- Private fields: `_camelCase` (underscore prefix)
- Local variables and parameters: `camelCase`
- Constants: `PascalCase`
- Use primary constructor for classes with dependencies or immutable properties
