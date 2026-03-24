# Add Request Tracking Skill

> Cross‑project note: replace placeholders like `{Entity}` and `{ProjectName}` with real names.

## When to use

Use this skill when endpoint-level telemetry, request logging, or Application Insights enrichment is required.

Typical cases:
- add request and response tracking to an endpoint
- add or update `WithApiTracking(...)`
- register telemetry middleware or endpoint filters
- enrich `RequestTelemetry` with business metadata

## Relevant instructions

Usually load:
- `instructions/Architecture.instructions.md`
- `instructions/Infrastructure.instructions.md`
- `instructions/WebApi.instructions.md`
- `instructions/Naming.instructions.md`
- `instructions/Testing.instructions.md` when tests are in scope

## Related patterns

Use as reference:
- `patterns/LogPatterns.md`
- `patterns/ApiPatterns.md`

## Inputs

- endpoint name
- epic name
- feature name
- whether request body capture is needed
- whether response body capture is needed
- any sensitive data restrictions

## Steps

1. Confirm the feature requires tracked telemetry
2. Keep telemetry implementation in Infrastructure and pipeline wiring in WebApi
3. Add or verify Application Insights registration in Infrastructure DI
4. Add or verify request logging middleware order in the WebApi pipeline
5. Add or verify endpoint metadata and endpoint filter wiring through `WithApiTracking(...)`
6. Capture only the telemetry needed for tracked endpoints
7. Avoid logging secrets or sensitive payload data
8. Suggest tests or verification steps for middleware order and telemetry behavior

## Output

- DI registration changes
- middleware pipeline changes
- endpoint tracking metadata
- filter and extension wiring
- test or verification recommendations

## Checklist

- WebApi only wires the pipeline
- Infrastructure owns implementation
- only tracked endpoints capture request bodies
- sensitive data is not logged unintentionally
- middleware order is correct
