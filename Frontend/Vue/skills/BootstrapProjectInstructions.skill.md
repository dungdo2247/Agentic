---
name: Bootstrap Project Instructions (Vue)
description: Scans the current Vue project codebase, detects tech stack and patterns, adapts generic instruction files, and generates project-specific .Project.Instructions.md files. Run as part of Tier 2 setup or standalone.
---

# Bootstrap Project Instructions (Vue)

## When to use

- Automatically invoked as part of the Tier 2 setup prompt from the README
- Or run standalone after pulling stack guidance into a project

---

## Workflow

### Step 1 — Scan the project codebase

Explore the project's source code to detect:

| What to detect        | How to detect                                                    |
| --------------------- | ---------------------------------------------------------------- |
| **Vue version**       | Check package.json for `vue` version                             |
| **Build tool**        | Vite (vite.config), Webpack (vue.config.js), Nuxt, etc.          |
| **State management**  | Pinia stores, Vuex stores, or composables-only                   |
| **Router**            | Vue Router config, file-based routing (Nuxt)                     |
| **CSS approach**      | Tailwind config, SCSS/Less usage, CSS Modules, component library |
| **Component library** | PrimeVue, Vuetify, Element Plus, Ant Design Vue, or none         |
| **API layer**         | Axios, fetch wrapper, ky, ofetch, etc.                           |
| **Testing**           | Vitest, Jest, Vue Test Utils, Cypress, Playwright                |
| **TypeScript**        | tsconfig.json, .ts/.vue files with `lang="ts"`                   |
| **Components**        | Component inventory: shared, feature, layout components          |
| **Folder structure**  | src/ layout, naming conventions, feature organization            |

### Step 2 — Fill in copilot-instructions.md

Update `.github/copilot-instructions.md` Project Guidelines with detected values.

### Step 3 — Adapt generic instruction files

Adapt the generic instruction content to match the project's actual patterns:

| If detected                            | Adapt                                                                                |
| -------------------------------------- | ------------------------------------------------------------------------------------ |
| **Options API**                        | `Components.instructions.md` → Options API structure instead of Composition API      |
| **SCSS instead of Tailwind**           | `Styling.instructions.md` → SCSS/BEM rules instead of Tailwind utilities             |
| **Vuex instead of Pinia**              | `Architecture.instructions.md` → Vuex modules instead of Pinia stores                |
| **Component library (PrimeVue, etc.)** | `Styling.instructions.md` → library theming rules                                    |
| **Nuxt**                               | `Architecture.instructions.md` → Nuxt conventions (auto-imports, file-based routing) |

### Step 4 — Generate .Project.Instructions.md files

Create project-specific files in `.github/instructions/`:

| File                                   | Content                                               |
| -------------------------------------- | ----------------------------------------------------- |
| `Architecture.Project.Instructions.md` | Routes, stores, API services, folder layout           |
| `Components.Project.Instructions.md`   | Component inventory, shared/feature/layout components |
| `Styling.Project.Instructions.md`      | Design tokens, theme config, CSS approach             |
| `Testing.Project.Instructions.md`      | Test setup, shared utilities, key scenarios           |

Each file must contain ONLY real data from the codebase — no leftover placeholders.

### Step 5 — Report

Show a summary of detected stack, adapted files, and generated `.Project.` files.
