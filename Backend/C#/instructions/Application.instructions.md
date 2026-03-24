---
name: Application Layer Instructions
description: Rules for organizing use-cases, validators, DTO ownership, and interface boundaries in the Application layer.
applyTo: "**/*.Application/**"
---

# Application Layer Instructions

## Purpose

This file defines rules for the Application layer.
It is the source of policy for use-case organization, validators, DTO ownership, and interface boundaries.

## Related Pattern Files

- `patterns/ApplicationPatterns.md`

## Architecture Patterns

The Application layer supports different organization patterns.
The actual pattern used in your project is defined in the project-specific `.Project.Instructions.md` files.

### Common patterns

| Pattern | Description |
|---|---|
| **CQRS + MediatR** | Commands and queries as separate request objects dispatched via MediatR. Each use-case has a handler and optional validator. |
| **Service Layer** | Business logic organized in service classes injected into controllers or endpoints. |
| **Vertical Slices** | Features organized in self-contained folders with handler, endpoint, validator, and DTOs co-located. |

> When `.Project.Instructions.md` files exist, follow the project-specific pattern described there.
> When they do not exist, follow the pattern detected in the codebase.

## Folder Structure

The folder structure varies by architecture pattern. Below is a representative structure:

| Folder | Purpose |
|---|---|
| `Common/` | Shared behaviors, interfaces, and cross-cutting concerns |
| `Common/Interfaces/` | Abstractions consumed by features and implemented in Infrastructure |
| `Features/{Feature}/` or `Services/` | Use-case handlers or service classes grouped by feature |
| `Utils/` | Shared application-level utilities |

> For the exact folder structure of your project, see `Application.Project.Instructions.md`.

## Universal Rules (apply to all patterns)

### Use-Case Organization

- Each use-case (read or write) should be clearly identifiable
- Use-case handlers or service methods must not call other handlers directly
- Application must not reference Infrastructure namespaces

### DTO Ownership

- Response DTOs belong in the Application layer, co-located with their feature or in a shared DTOs folder
- DTOs must not be placed in Domain
- Request/input models are co-located with their use-case handler or service

### Validation

- Validate all inputs at the application boundary
- Use a consistent validation approach across the project (FluentValidation, DataAnnotations, or manual)
- Validator naming follows `{Name}Validator.cs` and is co-located with its use-case
- Validation logic must not leak into Domain or Infrastructure

### Interfaces

- All infrastructure dependencies are abstracted behind interfaces in the Application layer
- Mirror folder and namespace structure to the implementing class in Infrastructure for easier navigation
- Interface naming uses `I{Name}`

### Authentication and Authorization

- The Application layer defines abstractions such as `IUserContext`
- Application may depend on user context abstractions, but not on direct ASP.NET Core HTTP or authentication framework types
