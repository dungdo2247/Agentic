---
name: Architecture Instructions (Vue)
description: Cross-cutting architecture reference for Vue projects describing folder structure, layer responsibilities, and dependency rules.
applyTo: "src/**"
---

# Architecture Instructions

## Folder Structure

| Folder                        | Purpose                                |
| ----------------------------- | -------------------------------------- |
| `src/components/`             | Reusable UI components                 |
| `src/components/shared/`      | Cross-feature shared components        |
| `src/components/{feature}/`   | Feature-scoped components              |
| `src/views/` or `src/pages/`  | Route-level page components            |
| `src/composables/`            | Reusable composition functions (hooks) |
| `src/stores/`                 | Pinia stores for state management      |
| `src/services/` or `src/api/` | API service layer                      |
| `src/types/`                  | TypeScript type definitions            |
| `src/utils/`                  | Pure utility functions                 |
| `src/assets/`                 | Static assets (images, fonts, icons)   |
| `src/router/`                 | Vue Router configuration               |
| `src/layouts/`                | Layout wrapper components              |

## Dependency Rules

- Components must not call API services directly — use composables or stores
- Stores must not import components
- Composables may use stores and services
- Services are the only layer that makes HTTP calls
- Types must not contain business logic
- Utils must be pure functions with no side effects

## Component Hierarchy

```
Layout → Page (view) → Feature Components → Shared Components
```

- Layouts define page structure (header, sidebar, footer)
- Pages orchestrate feature components and manage route-level concerns
- Feature components encapsulate specific business UI
- Shared components are generic and reusable across features

## State Management Rules

- Use Pinia for global state
- Prefer composables for local or feature-scoped state
- Keep stores thin — business logic belongs in composables or services
- Never mutate store state directly from components — use actions
