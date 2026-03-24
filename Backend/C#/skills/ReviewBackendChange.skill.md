# Review Backend Change Skill

> Cross‑project note: replace placeholders like `{Entity}` and `{ProjectName}` with real names.

## When to use

Use this skill when reviewing a backend change, pull request, or generated code.

Typical cases:
- review a new feature
- review a refactor
- review AI-generated code
- review architecture compliance
- review testing adequacy

## Relevant instructions

Load the instructions for the layers touched by the change.
Almost always include:
- `instructions/Architecture.instructions.md`
- `instructions/Naming.instructions.md`
- `instructions/Testing.instructions.md`

Then add the relevant layer instructions:
- `instructions/Domain.instructions.md`
- `instructions/Application.instructions.md`
- `instructions/Infrastructure.instructions.md`
- `instructions/WebApi.instructions.md`
- `instructions/Settings.instructions.md`

## Related patterns

Use only the patterns relevant to the touched code.

## Steps

1. Identify the impacted layers and main intent of the change
2. Review architecture boundaries first
3. Review correctness and behavior risks
4. Review naming and structure consistency
5. Review validation, authorization, and error handling
6. Review observability and operational concerns when relevant
7. Review test coverage and missing cases
8. Separate critical issues from optional improvements

## Output

- prioritized review findings
- explanation of why each issue matters
- concrete fixes or guidance
- suggested missing tests
- suggested guidance updates when needed

## Checklist

- architecture boundary violations are called out
- correctness issues are prioritized
- review is concrete, not vague
- test gaps are identified
- missing guidance is flagged
