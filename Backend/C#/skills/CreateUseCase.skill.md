# Create Use Case Skill

> Cross‑project note: replace placeholders like `{Entity}` and `{ProjectName}` with real names.

## When to use

Use this skill when the task adds or updates an Application command, query, handler, or validator.

Typical cases:
- create a command and handler
- create a query and handler
- add a validator
- change orchestration logic in Application
- add a DTO used by a feature

## Relevant instructions

Usually load:
- `instructions/Architecture.instructions.md`
- `instructions/Application.instructions.md`
- `instructions/Naming.instructions.md`
- `instructions/Testing.instructions.md`

Load `instructions/Domain.instructions.md` when business rules or domain concepts are involved.
Load `instructions/Infrastructure.instructions.md` when new interfaces or implementations are required.

## Related patterns

Use as reference:
- `patterns/ApplicationPatterns.md`
- `patterns/TestingPatterns.md`

## Inputs

- feature name
- read or write intent
- request fields
- expected result shape
- validation rules
- interfaces needed from Infrastructure

## Steps

1. Decide whether the use-case is a command or a query
2. Place the request, handler, and validator in the correct feature folder
3. Define or update response DTOs in the feature `Dtos` folder when needed
4. Add validation with FluentValidation
5. Depend only on Application interfaces, not Infrastructure implementations
6. Keep the handler focused on orchestration, not transport or persistence details
7. Suggest unit tests for handler and validator behavior

## Output

- request record
- handler
- validator
- DTOs when needed
- interface usage
- test recommendations

## Checklist

- command or query naming is correct
- handler does not call another handler directly
- validator is co-located
- Application does not reference Infrastructure namespaces
- DTO ownership is correct
