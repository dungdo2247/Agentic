---
name: WebApi Layer Instructions
description: Guidelines for API endpoints, OpenAPI setup, middleware, and WebApi layer boundaries.
applyTo: "**/*.WebApi/**"
---

# WebApi Layer Instructions

## Purpose

This file defines rules for the WebApi layer.
Use related patterns as reference only.
Do not move business or infrastructure implementation concerns into WebApi.

## Related Pattern Files

- `patterns/ApiPatterns.md`
- `patterns/LogPatterns.md`

## Folder Structure

| Folder | Purpose |
|---|---|
| `Endpoints/` or `Controllers/` | API endpoint definitions grouped by feature |
| `Extensions/` | `IServiceCollection` and `WebApplication` extension methods |
| `Middleware/` | Custom ASP.NET Core middleware (exception handling, authentication, etc.) |

> The project may use **Minimal API endpoints**, **MVC controllers**, or a mix.
> See `WebApi.Project.Instructions.md` for the actual style used.

## API Rules (apply to all styles)

- Route paths must be lowercase: `/api/v1/{resource}`
- Endpoints/actions are thin wrappers only — no business logic inside handlers
- Declare response types and error codes for OpenAPI documentation
- Group endpoints/controllers by feature or domain concept

### Minimal API specific

- `WithTags(...)` values must be PascalCase
- Use `TypedResults`, not `Results`
- Declare `.Produces<T>()` and `.ProducesProblem()` on every endpoint
- Group endpoints by feature using `MapGroup`

### MVC Controller specific

- Use `[ApiController]` attribute on all API controllers
- Use `[ProducesResponseType]` attributes for OpenAPI documentation
- Keep controllers thin — delegate to Application services or MediatR

## OpenAPI Rules

- Register OpenAPI services and middleware via extension methods
- Configure API reference UI (Scalar, Swagger UI, or similar)
- Configure API documentation with server URLs, info, and version
- Register operation transformers for required headers (e.g. API key)

## Middleware Rules

- Global exception handling goes in a dedicated class under `Middleware/`
- API key authentication (if used) is handled in middleware, not in endpoint filters
- All middleware is registered in `Program.cs` via extension methods
- Request logging middleware is added in the pipeline after `UseRouting()` and before `UseAuthentication()`
- Request logging middleware implementation remains in Infrastructure

## Authentication and Authorization

- Add service extensions for authentication schemes and authorization policies
- Keep auth configuration in dedicated extension methods under `Extensions/`
