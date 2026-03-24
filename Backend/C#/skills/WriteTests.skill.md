# Write Tests Skill

> Cross‑project note: replace placeholders like `{Entity}` and `{ProjectName}` with real names.

## When to use

Use this skill when the task adds or updates tests for domain, application, integration, or API behavior.

Typical cases:
- add handler unit tests
- add validator tests
- add domain invariant tests
- add integration tests for endpoints
- extend regression coverage after a bug fix

## Relevant instructions

Usually load:
- `instructions/Testing.instructions.md`
- `instructions/Naming.instructions.md`

Load layer instructions that match the code under test:
- `instructions/Domain.instructions.md`
- `instructions/Application.instructions.md`
- `instructions/Infrastructure.instructions.md`
- `instructions/WebApi.instructions.md`

## Related patterns

Use as reference:
- `patterns/TestingPatterns.md`
- `patterns/ApplicationPatterns.md`
- `patterns/ApiPatterns.md`

## Inputs

- subject under test
- behavior to verify
- desired test level
- dependencies to mock or keep real

## Steps

1. Choose the correct test level: unit or integration
2. Place the test in the correct test project
3. Name the test class and methods using project conventions
4. Keep setup minimal and deterministic
5. Mock only the dependencies that should be isolated
6. Cover success, failure, and edge cases relevant to the task
7. Avoid brittle assertions tied to incidental implementation details

## Output

- test class
- test cases
- any required builders or setup helpers
- coverage notes

## Checklist

- test level is appropriate
- naming is consistent
- tests are deterministic
- assertions are meaningful
- regression risk is covered
