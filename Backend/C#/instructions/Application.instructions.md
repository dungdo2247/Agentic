---
name: Application Layer Instructions
description: Rules for organizing CQRS features, validators, DTO ownership, and interface boundaries in the Application layer.
applyTo: "**/*.Application/**"
---

# Application Layer Instructions

## Purpose

This file defines rules for the Application layer.
It is the source of policy for CQRS organization, validators, DTO ownership, and interface boundaries.

## Related Pattern Files

- `patterns/ApplicationPatterns.md`

## Folder Structure

| Folder | Purpose |
|---|---|
| `Common/Behaviors/` | MediatR pipeline behaviors such as `ValidationBehavior` |
| `Common/Interfaces/` | Abstractions consumed by features and implemented in Infrastructure |
| `Features/{Feature}/Queries/{QueryName}/` | Query, handler, and validator co-located |
| `Features/{Feature}/Commands/{CommandName}/` | Command, handler, and validator co-located |
| `Features/{Feature}/Dtos/` | Response DTOs owned by that feature |
| `Utils/` | Shared application-level utilities |

## CQRS Rules

- Every use-case is either a Query for reads or a Command for writes
- Each query or command lives in its own sub-folder under `Queries/` or `Commands/`
- Each folder contains exactly three primary files:
  - `{Name}.cs` for the request record
  - `{Name}Handler.cs` for the MediatR handler
  - `{Name}Validator.cs` for the FluentValidation validator
- Handlers must not call other handlers directly
- Application must not reference Infrastructure namespaces

## DTO Ownership

- Response DTOs belong in `Features/{Feature}/Dtos/`
- DTOs must not be placed in Domain
- Request models are co-located with their handler

## Validation

- Use FluentValidation for all commands and queries
- Validation is auto-discovered by `ValidationBehavior<TRequest, TResponse>` and must not be invoked manually
- Validator naming follows `{Name}Validator.cs` and is co-located with its command or query

## Interfaces

- All infrastructure dependencies are abstracted behind interfaces in `Common/Interfaces/`
- Mirror folder and namespace structure to the implementing class in Infrastructure for easier navigation
- Interface naming uses `I{Name}`

## Authentication and Authorization

- The Application layer defines abstractions such as `IUserContext`
- Application may depend on user context abstractions, but not on direct ASP.NET Core HTTP or authentication framework types
