---
name: Architecture Overview Instructions
description: Cross-layer architecture reference describing project responsibilities, allowed dependencies, and boundary rules.
applyTo: "**"
---

# Architecture Instructions

## Purpose

This file defines cross-layer rules and responsibilities.
It is an instruction file, so it defines policy and boundaries.
Use related patterns only as implementation reference, not as policy.

## Layer Breakdown

### `src/` — Source Projects

| Project | Role |
| --- | --- |
| **Domain** | Core business models, enums, exceptions, and utilities. No dependencies on other layers. |
| **Application** | Use-cases: CQRS commands and queries, DTOs, pipeline behaviors, interfaces. Depends only on Domain. |
| **Infrastructure** | Technical implementations: persistence, caching, external service adapters, observability, telemetry. Implements interfaces defined in Application. |
| **WebApi** | Entry point: Minimal API endpoints, middleware, DI bootstrapping, OpenAPI configuration. Depends on Application and Infrastructure. |

## Dependency Rules

- Domain must not depend on Application, Infrastructure, or WebApi
- Application must not depend on Infrastructure or WebApi
- Infrastructure may depend on Application and Domain
- WebApi may depend on Application and Infrastructure
- Dependencies must point inward toward business logic, not outward toward transport or technical frameworks

## Boundary Rules

- Domain owns business rules and domain concepts
- Application coordinates use-cases and orchestration
- Infrastructure owns technical details and external integrations
- WebApi owns HTTP transport concerns only
- Keep DTOs, EF Core mappings, HTTP models, and middleware concerns out of Domain
- Keep business logic out of controllers, endpoints, and middleware

## Related Pattern Files

| Instruction focus | Pattern file |
| --- | --- |
| Project and folder layout | `patterns/StructurePatterns.md` |
| API endpoints and OpenAPI | `patterns/ApiPatterns.md` |
| CQRS handlers and validation | `patterns/ApplicationPatterns.md` |
| Repositories, caching, DI, HTTP adapters | `patterns/InfrastructurePatterns.md` |
| EF Core audit and configuration | `patterns/EntityFrameworkCorePatterns.md` |
| Background jobs and shared primitives | `patterns/CodePatterns.md` |
| Application Insights and request logging | `patterns/LogPatterns.md` |
| Unit and integration tests | `patterns/TestingPatterns.md` |
