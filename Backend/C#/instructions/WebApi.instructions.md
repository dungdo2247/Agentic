---
name: WebApi Layer Instructions
description: Guidelines for minimal API endpoints, OpenAPI setup, middleware, and WebApi layer boundaries.
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
| `Endpoints/` | Minimal API endpoint definitions grouped by feature |
| `Extensions/` | `IServiceCollection` and `WebApplication` extension methods |
| `Middleware/` | Custom ASP.NET Core middleware such as exceptions or API key authentication |

## Minimal API Rules

- Route group paths must be lowercase: `/api/v1/{domainmodel}`
- `WithTags(...)` values must be PascalCase
- Use `TypedResults`, not `Results`
- Declare `.Produces<T>()` and `.ProducesProblem()` on every endpoint
- Endpoints are thin wrappers only, with no business logic inside handlers
- Group endpoints by feature using `MapGroup`

## OpenAPI Rules

- Register via `AddOpenApiServices()` and `UseOpenApiPipeline()` extension methods
- `OpenApiExtensions.cs` lives in `{ProjectName}.WebApi/Extensions/`
- Use Scalar for API reference UI, not Swagger UI
- Call `UseOpenApiPipeline()` after `UseAuthentication()` and `UseAuthorization()`
- Register a document transformer to configure `Servers` and `Info`
- Register `AddApiHeaderParameter` as an operation transformer for the API key header

## Middleware Rules

- Global exception handling goes in a dedicated class under `Middleware/`
- API key authentication is handled in middleware, not in endpoint filters
- All middleware is registered in `Program.cs` via extension methods
- Request logging middleware is added in the pipeline after `UseRouting()` and before `UseAuthentication()`
- Request logging middleware implementation remains in Infrastructure under `Infrastructure/ApplicationInsights/`

## Authentication and Authorization

- Add service extensions for authentication schemes and authorization policies in `AuthenticationExtensions.cs` under `Extensions/`
