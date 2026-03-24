---
name: Naming Conventions Instructions (Vue)
description: Standard naming patterns for files, components, composables, stores, and TypeScript identifiers in Vue projects.
applyTo: "src/**"
---

# Naming Conventions

## File Naming

| Type       | Convention                           | Example               |
| ---------- | ------------------------------------ | --------------------- |
| Component  | `PascalCase.vue`                     | `UserProfile.vue`     |
| Composable | `use{Name}.ts`                       | `useAuth.ts`          |
| Store      | `use{Name}Store.ts`                  | `useUserStore.ts`     |
| Service    | `{name}Service.ts`                   | `authService.ts`      |
| Type file  | `{name}.types.ts`                    | `user.types.ts`       |
| Utility    | `{name}.ts`                          | `formatDate.ts`       |
| Test       | `{Name}.spec.ts` or `{Name}.test.ts` | `UserProfile.spec.ts` |
| Page/View  | `{Name}Page.vue` or `{Name}View.vue` | `DashboardPage.vue`   |

## TypeScript Identifiers

- Interfaces and types: `PascalCase` (prefix `I` is optional, prefer no prefix)
- Variables and functions: `camelCase`
- Constants: `UPPER_SNAKE_CASE` for true constants, `camelCase` for config objects
- Enums: `PascalCase` for name, `PascalCase` for values
- Props: `camelCase`
- Emits: `camelCase`

## Folder Naming

- All folders: `kebab-case` or `camelCase` (choose one and be consistent)
- Feature folders: named after the feature/domain concept
