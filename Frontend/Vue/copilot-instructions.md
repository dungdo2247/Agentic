# Copilot Instructions — Vue + Tailwind (Cross‑Project Template)

## Purpose

This file is the lightweight entry point for Vue frontend guidance.

## Project Guidelines (customize)

- Framework: Vue `{VueVersion}` (example: 3)
- CSS framework: `{CSSFramework}` (example: Tailwind CSS)
- State management: `{StateManagement}` (example: Pinia)
- Router: `{Router}` (example: Vue Router)
- Build tool: `{BuildTool}` (example: Vite)
- API layer: `{APILayer}` (example: Axios)
- Testing: `{TestFramework}` (example: Vitest + Vue Test Utils)
- Component library: `{ComponentLibrary}` (example: none, PrimeVue, Vuetify)

> These values are filled in automatically when you run the Tier 2 setup prompt from the README.
> The setup scans your codebase, adapts instruction files to your stack, and generates
> project-specific `.Project.Instructions.md` files — all in one step.

## Required Workflow

1. Follow `agents/Frontend.agent.md`
2. Identify the task type and impacted areas
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
- Apply only safe, behavior-preserving fixes unless explicitly asked for broader refactoring
