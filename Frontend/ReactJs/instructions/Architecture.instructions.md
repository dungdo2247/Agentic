---
name: Architecture Instructions (ReactJS)
description: Cross-cutting architecture reference for ReactJS projects describing folder structure, layer responsibilities, and dependency rules.
applyTo: "src/**"
---

# Architecture Instructions

## Folder Structure

| Folder | Purpose |
|---|---|
| `src/components/` | Reusable UI components |
| `src/components/shared/` | Cross-feature shared components |
| `src/components/{feature}/` | Feature-scoped components |
| `src/pages/` | Route-level page components |
| `src/hooks/` | Custom React hooks |
| `src/models/` | State management models (dva/zustand/Redux) |
| `src/services/` | API service layer |
| `src/types/` or `src/typings/` | TypeScript type definitions |
| `src/utils/` | Pure utility functions |
| `src/assets/` | Static assets (images, fonts, icons) |
| `src/layouts/` | Layout wrapper components |
| `src/locales/` | Internationalization files |
| `src/access.ts` | Access control / permissions |

## Dependency Rules

- Components must not call API services directly — use hooks or models
- Models/stores must not import components
- Custom hooks may use models and services
- Services are the only layer that makes HTTP calls
- Types must not contain business logic
- Utils must be pure functions with no side effects

## Component Hierarchy

```
Layout → Page → Feature Components → Shared Components
```

- Layouts define page structure (header, sidebar, footer)
- Pages orchestrate feature components and manage route-level concerns
- Feature components encapsulate specific business UI
- Shared components are generic and reusable across features

## State Management Rules

- Use the project's chosen state management (dva / zustand / Redux Toolkit)
- Keep models/stores thin — business logic belongs in hooks or services
- Prefer hooks for local or feature-scoped state
- Use selectors for derived state instead of computing in components
