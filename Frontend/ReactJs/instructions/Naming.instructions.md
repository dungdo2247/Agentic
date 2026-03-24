---
name: Naming Conventions Instructions (ReactJS)
description: Standard naming patterns for files, components, hooks, models, and TypeScript identifiers in ReactJS projects.
applyTo: "src/**"
---

# Naming Conventions

## File Naming

| Type        | Convention                                       | Example                   |
| ----------- | ------------------------------------------------ | ------------------------- |
| Component   | `PascalCase.tsx`                                 | `UserProfile.tsx`         |
| Hook        | `use{Name}.ts`                                   | `useAuth.ts`              |
| Model/Store | `{name}Model.ts` or `use{Name}Store.ts`          | `userModel.ts`            |
| Service     | `{name}Service.ts`                               | `authService.ts`          |
| Type file   | `{name}.d.ts` or `{name}.types.ts`               | `user.d.ts`               |
| Utility     | `{name}.ts`                                      | `formatDate.ts`           |
| Test        | `{Name}.test.tsx` or `{Name}.spec.tsx`           | `UserProfile.test.tsx`    |
| Page        | `index.tsx` (Umi convention) or `{Name}Page.tsx` | `index.tsx`               |
| CSS Module  | `{Name}.module.less` or `{Name}.module.css`      | `UserProfile.module.less` |

## TypeScript Identifiers

- Interfaces and types: `PascalCase` (prefix `I` is optional, prefer no prefix)
- Variables and functions: `camelCase`
- Constants: `UPPER_SNAKE_CASE` for true constants, `camelCase` for config objects
- Enums: `PascalCase` for name, `PascalCase` for values
- Props interfaces: `{ComponentName}Props`
- Event handlers: `handle{Event}` or `on{Event}`

## Folder Naming

- All folders: `kebab-case` or `camelCase` (choose one and be consistent)
- Feature folders: named after the feature/domain concept
- Umi pages: follow Umi file-based routing convention
