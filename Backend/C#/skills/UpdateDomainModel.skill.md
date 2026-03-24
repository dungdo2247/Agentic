# Update Domain Model Skill

> Cross‑project note: replace placeholders like `{Entity}` and `{ProjectName}` with real names.

## When to use

Use this skill when the task changes entities, value objects, enums, or domain rules.

Typical cases:
- add or update a domain entity
- add an enum or value object
- add domain invariants
- update project-specific domain relationships

## Relevant instructions

Usually load:
- `instructions/Architecture.instructions.md`
- `instructions/Domain.instructions.md`
- `instructions/Naming.instructions.md`

Load `instructions/Domain.Project.Instructions.md` when project-specific business rules matter.
Load `instructions/Testing.instructions.md` when tests are in scope.

## Related patterns

Use as reference:
- `patterns/CodePatterns.md`
- `patterns/EntityFrameworkCorePatterns.md` only when persistence implications must be understood

## Inputs

- business concept
- required properties
- invariants or rules
- relationships to other domain types
- persistence impact if known

## Steps

1. Confirm the change belongs in Domain and not in Application, Infrastructure, or WebApi
2. Update the domain type and its invariants
3. Keep framework and persistence concerns out of Domain
4. Validate relationships with existing entities, value objects, and enums
5. Note any downstream impact on Application handlers, EF Core configuration, or API contracts
6. Suggest unit tests for invariants and behavior

## Output

- domain model changes
- invariant updates
- enum or value object additions
- downstream impact notes
- test recommendations

## Checklist

- Domain remains framework-agnostic
- no DTOs or EF Core mapping leak into Domain
- business rules are explicit
- project-specific rules are respected
