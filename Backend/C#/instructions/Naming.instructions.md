---
name: Naming Conventions Instructions
description: Standard naming patterns for projects, CQRS artifacts, endpoints, files, and C# identifiers.
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

## CQRS Artifacts

| Type | Pattern | Example |
|---|---|---|
| Query | `{Name}Query` | `Get{Entity}Query` |
| Query Handler | `{Name}QueryHandler` | `Get{Entity}QueryHandler` |
| Query Validator | `{Name}QueryValidator` | `Get{Entity}QueryValidator` |
| Command | `{Name}Command` | `Create{Entity}Command` |
| Command Handler | `{Name}CommandHandler` | `Create{Entity}CommandHandler` |
| Command Validator | `{Name}CommandValidator` | `Create{Entity}CommandValidator` |

## DTOs

| Type | Pattern | Example |
|---|---|---|
| Response DTO | `{Name}Dto` | `{Entity}Dto` |

## Endpoints

| Convention | Rule | Example |
|---|---|---|
| Route path | lowercase | `/api/v1/{entity}` |
| `WithTags` | PascalCase | `"{Entity}"` |
| Endpoint name | `Get{DomainModel}ById` | `"Get{Entity}ById"` |
| Endpoint class | `{DomainModel}Endpoints` | `{Entity}Endpoints` |
| Map method | `Map{DomainModel}Endpoints` | `Map{Entity}Endpoints` |

## File Naming

| Type | Pattern | Example |
|---|---|---|
| Validator | `{Name}Validator.cs` | `Create{Entity}CommandValidator.cs` |
| EF Core config | `{Entity}Configuration.cs` | `{Entity}Configuration.cs` |
| Repository | `{Entity}Repository.cs` | `{Entity}Repository.cs` |
| Interface | `I{Name}.cs` | `I{Entity}Repository.cs` |
| Extension class | `{Purpose}Extensions.cs` | `OpenApiExtensions.cs` |
| Cache service | `{Entity}CacheService.cs` | `{Entity}CacheService.cs` |
| Background job | `{Purpose}Job.cs` | `ProcessDocumentJob.cs` |

## General C# Rules

- Classes, methods, properties: `PascalCase`
- Private fields: `_camelCase` (underscore prefix)
- Local variables and parameters: `camelCase`
- Constants: `PascalCase`
- Use primary constructor for classes with dependencies or immutable properties
