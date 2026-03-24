# Refactor Backend Feature Skill

> Cross‑project note: replace placeholders like `{Entity}` and `{ProjectName}` with real names.

## When to use

Use this skill when improving structure, readability, maintainability, or boundaries without changing intended behavior.

Typical cases:
- simplify a feature flow
- remove duplication
- improve naming
- move code to the correct layer
- replace weak abstractions with clearer ones

## Relevant instructions

Load the instructions for the layers touched by the refactor.
Almost always include:
- `instructions/Architecture.instructions.md`
- `instructions/Naming.instructions.md`
- `instructions/Testing.instructions.md`

## Related patterns

Use only the patterns relevant to the code being refactored.

## Steps

1. Identify the current pain points and the target shape
2. Confirm intended behavior that must be preserved
3. Move code to the correct layer when boundaries are wrong
4. Simplify control flow, improve naming, and remove duplication
5. Remove abstractions that add indirection without value
6. Keep tests or add regression coverage for risky areas
7. Note any follow-up guidance that should be documented

## Output

- refactor plan
- structural improvements
- behavior-preserving code changes
- regression test suggestions
- guidance update suggestions

## Checklist

- behavior is preserved
- readability improves
- boundaries improve
- duplication is reduced
- test coverage is still sufficient
