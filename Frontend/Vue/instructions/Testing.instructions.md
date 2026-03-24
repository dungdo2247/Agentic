---
name: Testing Instructions (Vue)
description: Standards for component, composable, and integration test structure in Vue projects.
applyTo: "**/*.spec.ts,**/*.test.ts"
---

# Testing Instructions

## Test Structure

| Project / Folder         | Purpose                           |
| ------------------------ | --------------------------------- |
| `src/**/*.spec.ts`       | Unit tests co-located with source |
| `tests/` or `__tests__/` | Integration or E2E tests          |

## Unit Test Rules

- One test file per component, composable, or utility
- Test file naming: `{Name}.spec.ts` (co-located) or `{Name}.test.ts`
- Test method naming: `it('should {expected behavior} when {condition}')`
- Use Vue Test Utils for component mounting
- Use the Vitest (or Jest) testing API
- Mock API calls and external dependencies

## Component Test Rules

- Test rendered output, not implementation details
- Test user interactions (clicks, inputs, form submissions)
- Test prop variations and edge cases
- Test emitted events
- Use `mount()` for full component tests, `shallowMount()` for isolation

## Composable Test Rules

- Test composables in isolation using `renderHook` or a wrapper component
- Test reactive state changes
- Test computed values
- Mock dependencies (stores, services)

## General Rules

- Tests must be deterministic — no random data, no real API calls
- Prefer `it.each()` for data-driven scenarios
- Keep test setup minimal — use factories for complex test data
- Arrange-Act-Assert pattern for all tests
