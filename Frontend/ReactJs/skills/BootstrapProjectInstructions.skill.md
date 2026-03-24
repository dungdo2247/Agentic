---
name: Bootstrap Project Instructions (ReactJS)
description: Scans the current ReactJS project codebase, detects tech stack and patterns, adapts generic instruction files, and generates project-specific .Project.Instructions.md files. Run as part of Tier 2 setup or standalone.
---

# Bootstrap Project Instructions (ReactJS)

## When to use

- Automatically invoked as part of the Tier 2 setup prompt from the README
- Or run standalone after pulling stack guidance into a project

---

## Workflow

### Step 1 — Scan the project codebase

Explore the project's source code to detect:

| What to detect       | How to detect                                                           |
| -------------------- | ----------------------------------------------------------------------- |
| **React version**    | Check package.json for `react` version                                  |
| **Meta framework**   | Umi (.umirc.ts), Next.js (next.config), CRA (react-scripts), Vite, etc. |
| **State management** | dva models, zustand stores, Redux slices, React context, etc.           |
| **Router**           | Umi file-based routing, React Router config, Next.js pages              |
| **UI library**       | Ant Design (antd), Material UI, Chakra, or none                         |
| **CSS approach**     | CSS Modules, Less, SCSS, styled-components, Tailwind, etc.              |
| **API layer**        | umi-request, Axios, fetch wrapper, React Query, SWR                     |
| **Testing**          | Jest, React Testing Library, Vitest, Cypress, Playwright                |
| **TypeScript**       | tsconfig.json, .tsx files                                               |
| **Components**       | Component inventory: shared, feature, layout components                 |
| **Folder structure** | src/ layout, naming conventions, feature organization                   |

### Step 2 — Fill in copilot-instructions.md

Update `.github/copilot-instructions.md` Project Guidelines with detected values.

### Step 3 — Adapt generic instruction files

Adapt the generic instruction content to match the project's actual patterns:

| If detected              | Adapt                                                                                |
| ------------------------ | ------------------------------------------------------------------------------------ |
| **Class components**     | `Components.instructions.md` → class component structure instead of functional       |
| **Material UI / Chakra** | `Styling.instructions.md` → library theming instead of Ant Design                    |
| **Redux**                | `Architecture.instructions.md` → Redux store/slices instead of dva models            |
| **Next.js**              | `Architecture.instructions.md` → Next.js conventions (app router, server components) |
| **Tailwind**             | `Styling.instructions.md` → Tailwind utilities instead of CSS Modules + Less         |

### Step 4 — Generate .Project.Instructions.md files

Create project-specific files in `.github/instructions/`:

| File                                   | Content                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| `Architecture.Project.Instructions.md` | Routes, stores/models, API services, folder layout           |
| `Components.Project.Instructions.md`   | Component inventory, shared/feature/layout, UI library usage |
| `Styling.Project.Instructions.md`      | Theme config, design tokens, CSS approach                    |
| `Testing.Project.Instructions.md`      | Test setup, mocks, shared utilities, key scenarios           |

Each file must contain ONLY real data from the codebase — no leftover placeholders.

### Step 5 — Report

Show a summary of detected stack, adapted files, and generated `.Project.` files.
