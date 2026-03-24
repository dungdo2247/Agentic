# Create Endpoint Skill

> Cross‑project note: replace placeholders like `{Entity}` and `{ProjectName}` with real names.

## When to use

Use this skill when the task adds or updates an HTTP endpoint in the WebApi layer.

Typical cases:
- create a new Minimal API endpoint
- add a route to an existing endpoint group
- add OpenAPI metadata
- add request or response contracts
- wire an endpoint to an Application command or query

## Relevant instructions

Usually load:
- `instructions/Architecture.instructions.md`
- `instructions/WebApi.instructions.md`
- `instructions/Application.instructions.md`
- `instructions/Naming.instructions.md`
- `instructions/Testing.instructions.md` when tests are in scope

## Related patterns

Use as reference:
- `patterns/ApiPatterns.md`
- `patterns/ApplicationPatterns.md`
- `patterns/TestingPatterns.md`

## Inputs

- feature name
- route and HTTP method
- request model or parameters
- expected response model
- authorization requirements
- error cases
- whether a command or query is needed

## Steps

1. Confirm the endpoint belongs in WebApi and business logic belongs outside the endpoint
2. Define or identify the Application command or query that the endpoint should call
3. Create or update the endpoint group and route
4. Apply endpoint naming, route casing, tags, and OpenAPI metadata
5. Return `TypedResults` and declare `.Produces<T>()` and `.ProducesProblem()`
6. Ensure validation is handled in Application, not inside endpoint business logic
7. Add or update authorization and API key requirements when relevant
8. Suggest tests for success, validation failures, and not-found or unauthorized paths

## Output

- endpoint mapping
- request and response contract wiring
- OpenAPI metadata
- command or query invocation
- test recommendations

## Checklist

- endpoint is thin
- route path is lowercase
- tags are PascalCase
- `TypedResults` is used
- OpenAPI metadata is declared
- business logic is not embedded in WebApi
