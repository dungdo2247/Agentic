---
name: Testing Instructions (ReactJS)
description: Standards for component, hook, and integration test structure in ReactJS projects.
applyTo: "**/*.test.tsx,**/*.test.ts,**/*.spec.tsx,**/*.spec.ts"
---

# Testing Instructions

## Test Structure

| Folder                   | Purpose                           |
| ------------------------ | --------------------------------- |
| `src/**/*.test.tsx`      | Unit tests co-located with source |
| `tests/` or `__tests__/` | Integration or E2E tests          |
| `mock/`                  | Mock data and API mocks           |

## Unit Test Rules

- One test file per component, hook, or utility
- Test file naming: `{Name}.test.tsx` (co-located)
- Test method naming: `it('should {expected behavior} when {condition}')`
- Use React Testing Library for component testing
- Use `renderHook` from `@testing-library/react-hooks` for custom hooks
- Mock API calls and external dependencies

## Component Test Rules

- Test rendered output, not implementation details
- Test user interactions (clicks, inputs, form submissions)
- Test prop variations and edge cases
- Use `screen` and `userEvent` from React Testing Library
- Avoid testing Ant Design component internals

## Hook Test Rules

- Test hooks in isolation using `renderHook`
- Test state transitions
- Test side effects
- Mock dependencies (stores, services)

## General Rules

- Tests must be deterministic — no random data, no real API calls
- Prefer `it.each()` for data-driven scenarios
- Keep test setup minimal — use factories for complex test data
- Arrange-Act-Assert pattern for all tests
