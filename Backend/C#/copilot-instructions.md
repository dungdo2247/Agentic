# Copilot Instructions (Cross‑Project Template)

## Purpose

This file is the lightweight entry point for repository guidance.

## Project Guidelines (customize)

- Language/runtime: .NET `{DotNetVersion}` (example: 10)
- Database: `{Database}` (example: Postgres)
- Architecture: `{Architecture}` (example: Clean Architecture + CQRS, MVC, Vertical Slices)
- API style: `{ApiStyle}` (example: Minimal API, MVC Controllers)
- Background jobs: `{JobRunner}` (example: Hangfire)
- Auth scheme: `{AuthScheme}` (example: JWT Bearer)
- File storage: `{StorageProvider}` (example: Azure Blob Storage)
- Validation: `{ValidationLibrary}` (example: FluentValidation, DataAnnotations)
- Mocking: `{MockingLibrary}` (example: NSubstitute, Moq)
- Assertions: `{AssertionLibrary}` (example: FluentAssertions, xUnit built-in)

> These values are filled in automatically when you run the Tier 2 setup prompt from the README.
> The setup scans your codebase, adapts instruction and pattern files to your architecture, and generates
> project-specific `.Project.Instructions.md` files — all in one step.

## Required Workflow

1. Follow `agents/Backend.agent.md`
2. Identify the task type and impacted layers
3. Load only the relevant instruction files
4. Load only the relevant skill files
5. Use the relevant pattern files as reference
6. For non-trivial work, provide a concise plan first
7. When the workflow requires approval before changes, wait for approval before implementing

## Precedence

When guidance overlaps or conflicts, use this order:

1. instructions
2. skills
3. patterns

Instructions define policy.
Skills define execution workflows.
Patterns provide implementation references.

## Review and Change Rules

- Do not assume all instruction files are relevant
- Do not duplicate instruction policy inside skills
- Do not let patterns override instructions
- If guidance is missing or contradictory, call it out and suggest an update
- Apply only safe, behavior-preserving IDE or Roslyn fixes unless explicitly asked for broader refactoring
- Avoid introducing newer syntax only for style if the repository does not already use it
