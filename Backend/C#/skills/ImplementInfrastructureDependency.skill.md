# Implement Infrastructure Dependency Skill

> Cross‑project note: replace placeholders like `{Entity}` and `{ProjectName}` with real names.

## When to use

Use this skill when the task adds or updates persistence, caching, storage, external API integration, telemetry wiring, or other Infrastructure implementations.

Typical cases:
- implement an Application interface
- add an EF Core repository or configuration
- add a typed HTTP client or adapter
- add blob or file storage implementation
- add cache or background job support

## Relevant instructions

Usually load:
- `instructions/Architecture.instructions.md`
- `instructions/Infrastructure.instructions.md`
- `instructions/Naming.instructions.md`
- `instructions/Testing.instructions.md`

Load `instructions/Application.instructions.md` when new interfaces or options are defined there.
Load `instructions/Settings.instructions.md` when configuration changes are needed.

## Related patterns

Use as reference:
- `patterns/InfrastructurePatterns.md`
- `patterns/EntityFrameworkCorePatterns.md`
- `patterns/CodePatterns.md`
- `patterns/SettingsPatterns.md` when options are involved

## Inputs

- interface or technical concern to implement
- persistence or external service details
- configuration needs
- resiliency or observability needs

## Steps

1. Confirm the implementation belongs in Infrastructure
2. Implement the Application interface or technical component in the correct folder
3. Register the dependency in Infrastructure DI
4. Keep Application free from Infrastructure types and concerns
5. Add configuration or options binding if needed
6. Consider logging, telemetry, retries, and error mapping where relevant
7. Suggest integration tests or targeted unit tests

## Output

- Infrastructure implementation
- DI registration
- settings updates when needed
- test recommendations

## Checklist

- implementation stays in Infrastructure
- Application only depends on abstraction
- DI registration is added
- external failures are mapped intentionally
- configuration is strongly typed when needed
