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
| `{ProjectName}.Application.Tests` | Unit tests for handlers, validators, pipeline behaviors — mocked interfaces |
| `{ProjectName}.IntegrationTests` | End-to-end tests against real or in-memory infrastructure |

## Unit Test Rules

- One test class per subject (handler, validator, domain model)
- Test class naming: `{SubjectUnderTest}Tests`
- Test method naming: `{Method}_When{Condition}_Should{ExpectedOutcome}`
- Use **NSubstitute** for mocking interfaces in Application tests
- **Never instantiate Infrastructure classes** in Application.Tests
- Use **FluentAssertions** for readable assertions

## Application Test Rules

- Mock all interfaces from `Application/Common/Interfaces/`
- Test handlers in isolation — mock repository, verify returned DTO
- Test validators using `TestValidate()` from `FluentValidation.TestHelper`

## Integration Test Rules

- Use `WebApplicationFactory<T>` for full API integration tests
- Use a dedicated test database or in-memory equivalent
- Reset state between test runs — no shared mutable state
- Cover the full slice: HTTP request → handler → infrastructure

## General Rules

- Tests must be deterministic — no `DateTime.Now` without abstraction, no random data
- Prefer `[Theory]` + `[InlineData]` for data-driven scenarios
- Keep test setup minimal — use builders or factories for complex test objects
