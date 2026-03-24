---
name: Testing Instructions
description: Standards for unit and integration test structure, naming, isolation, and assertion style.
applyTo: "tests/**"
---

# Testing Instructions

## Related Pattern Files

- `patterns/TestingPatterns.md`

## Project Structure

| Project | Purpose |
|---|---|
| `{ProjectName}.Domain.Tests` | Unit tests for domain models, value objects, business rules |
| `{ProjectName}.Application.Tests` | Unit tests for handlers or services, validators, pipeline behaviors — mocked interfaces |
| `{ProjectName}.IntegrationTests` | End-to-end tests against real or in-memory infrastructure |

## Unit Test Rules

- One test class per subject (handler, service, validator, domain model)
- Test class naming: `{SubjectUnderTest}Tests`
- Test method naming: `{Method}_When{Condition}_Should{ExpectedOutcome}`
- Use a mocking library for interfaces in Application tests
- **Never instantiate Infrastructure classes** in Application.Tests
- Use fluent assertion style for readable assertions

## Application Test Rules

- Mock all interfaces from the Application layer
- Test handlers or services in isolation — mock repository, verify returned DTO
- Test validators using the test utilities provided by your validation framework

## Integration Test Rules

- Use `WebApplicationFactory<T>` for full API integration tests
- Use a dedicated test database or in-memory equivalent
- Reset state between test runs — no shared mutable state
- Cover the full slice: HTTP request → handler/service → infrastructure

## General Rules

- Tests must be deterministic — no `DateTime.Now` without abstraction, no random data
- Prefer parameterized tests for data-driven scenarios
- Keep test setup minimal — use builders or factories for complex test objects

> For project-specific test frameworks and conventions, see `Testing.Project.Instructions.md`.
