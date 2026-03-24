# Frontend Guidance — ReactJS + Umi + Ant Design

AI guidance for ReactJS + Umi + Ant Design projects using GitHub Copilot.

Stack: **React**, **Umi**, **Ant Design**

---

## Two-tier model

| Tier              | Content                                 | Where it lives                                                         | When                         |
| ----------------- | --------------------------------------- | ---------------------------------------------------------------------- | ---------------------------- |
| **User space**    | `Frontend.agent.md` (orchestrator)      | `~/.github/agents/`                                                    | Installed once per machine   |
| **Project space** | `instructions/`, `skills/`, `patterns/` | `{repo}/.github/instructions/`, `.github/skills/`, `.github/patterns/` | Pulled per project on demand |

---

## Tier 1 — Install the agent to user space (one-time)

> See the full prompt in the [Backend C# README](../../Backend/C%23/README.md) under **Tier 1**.

After running, the result in user space will be:

```
~/.github/
  copilot-instructions.md
  agents/
    Backend.agent.md
    Frontend-vue.agent.md
    Frontend-reactjs.agent.md
```

---

## Tier 2 — Pull stack guidance into a project (per repo)

Copy the prompt below and paste it into any AI chat.

The AI will clone the repo, copy generic guidance files, **scan your project's codebase**,
detect the tech stack and patterns, adapt instruction content, and generate
project-specific `.Project.Instructions.md` files — all automatically.

```
I want to set up ReactJS frontend AI guidance for this project from {public_github}.

Please do the following steps for me:

### Step 1 — Clone and copy

1. Clone the repo with a shallow clone (--depth 1) into a temporary directory.

2. Create the following folders in the current workspace root if they do not exist:
   - .github/instructions/
   - .github/skills/
   - .github/patterns/

3. Copy these files from the cloned repo (do NOT copy any *.Project.Instructions.md files):
   - Frontend/ReactJs/instructions/*.instructions.md  ->  .github/instructions/
   - Frontend/ReactJs/skills/*                        ->  .github/skills/
   - Frontend/ReactJs/patterns/*                      ->  .github/patterns/
   - Frontend/ReactJs/copilot-instructions.md         ->  .github/copilot-instructions.md

4. Delete the temporary clone directory completely.

### Step 2 — Scan my codebase

Now analyze my project's source code to detect:

a. **Tech stack** — scan package.json, config files, and source code:
   - React version
   - Meta framework (Umi, Next.js, CRA, Vite, Remix, etc.)
   - UI library (Ant Design, Material UI, Chakra UI, none, etc.)
   - State management (dva, Redux Toolkit, zustand, Jotai, MobX, none, etc.)
   - Router (Umi Router, React Router, file-based routing, etc.)
   - Build tool (Umi built-in, Webpack, Vite, etc.)
   - API layer (umi-request, Axios, fetch wrapper, TanStack Query, etc.)
   - Testing framework (Jest, Vitest, React Testing Library, Cypress, Playwright, etc.)
   - CSS approach (CSS Modules, Less, SCSS, Tailwind, styled-components, Ant Design tokens, etc.)
   - TypeScript or JavaScript

b. **Project patterns** — scan the source directory:
   - Folder structure and naming conventions
   - Component organization (flat, feature folders, atomic design, etc.)
   - How routes are defined (config-based, file-based, etc.)
   - How models/stores are organized (dva models, Redux slices, etc.)
   - How API calls are structured (services layer, inline, etc.)

### Step 3 — Fill in copilot-instructions.md

Open .github/copilot-instructions.md and replace all `{placeholder}` values in the
"Project Guidelines" section with the actual values detected in Step 2.

### Step 4 — Adapt generic instruction files

Review each .github/instructions/*.instructions.md file and adapt its content
to match my project's actual tech stack and patterns from Step 2.

Key adaptations:
- If my project uses **Next.js** (not Umi): rewrite Architecture.instructions.md for Next.js
  conventions (App Router, Server Components, file-based routing, etc.).
- If my project uses **Redux Toolkit** (not dva): adapt Architecture.instructions.md for
  Redux slices, RTK Query, etc.
- If my project uses **Material UI** (not Ant Design): rewrite Styling.instructions.md
  for MUI theming, sx prop, styled API, etc.
- If my project uses **class components**: adapt Components.instructions.md accordingly.
- Adapt Testing.instructions.md to match my actual test framework and conventions.

Do NOT change Naming.instructions.md unless the project clearly uses different conventions.

### Step 5 — Generate .Project.Instructions.md files

Scan my actual source code and generate these files in .github/instructions/:

- **Architecture.Project.Instructions.md** — list the actual folder structure, route definitions,
  models/stores, API layer setup, and key architectural decisions found in my codebase.
- **Components.Project.Instructions.md** — list all shared/common components, layout components,
  feature components, custom hooks, and their props/interfaces as found in the codebase.
- **Styling.Project.Instructions.md** — list the actual theme configuration, design tokens,
  CSS variables, Less/SCSS variables, breakpoints, and color palette from my project.
- **Testing.Project.Instructions.md** — list the test setup, test utilities, custom render
  helpers, mock patterns, and key test scenarios found in my test files.

Each file should contain actual names, paths, and patterns from MY codebase — not
generic placeholders.

### Step 6 — Summary

Show me a summary of:
- Detected tech stack
- Which generic instruction files were adapted (and what changed)
- Which .Project.Instructions.md files were generated
- Anything that needs manual review or was unclear
```

### Result in project space

```
{workspaceRoot}/
  .github/
    copilot-instructions.md          ← filled in with your project's tech stack
    instructions/
      Architecture.instructions.md   ← adapted to your patterns
      Components.instructions.md     ← adapted to your component style
      Naming.instructions.md
      Styling.instructions.md        ← adapted to your CSS/UI approach
      Testing.instructions.md        ← adapted to your test framework
      *.Project.Instructions.md      ← generated from your codebase
    skills/
      ...
    patterns/
      ...
```

---

## How to use

Use natural language in Copilot Chat:

| Task                   | What to say                                          |
| ---------------------- | ---------------------------------------------------- |
| Create a new component | _"Create a UserCard component with Ant Design Card"_ |
| Create a new page      | _"Create a dashboard page with Umi route"_           |
| Create a custom hook   | _"Create a hook for pagination"_                     |
| Write tests            | _"Write tests for the UserCard component"_           |
| Review a change        | _"Review this component for architecture issues"_    |

---

## Re-generate project-specific instructions

If your project evolves (new components, routes, etc.), paste this into Copilot Chat:

```
Re-scan my codebase and update all .Project.Instructions.md files in .github/instructions/
to reflect the current state of the project.

For each .Project.Instructions.md file:
- Re-read the actual source code for that area
- Update the file with current components, routes, models, styles, tests, etc.
- Keep the generic *.instructions.md files unchanged

Show me what changed in each file.
```

---

## Structure

```
Frontend/ReactJs/
  agents/
    Frontend.agent.md
  instructions/
    Architecture.instructions.md
    Components.instructions.md
    Naming.instructions.md
    Styling.instructions.md
    Testing.instructions.md
  skills/
    CreateComponent.skill.md
    CreatePage.skill.md
    CreateHook.skill.md
    WriteTests.skill.md
  patterns/
    ComponentPatterns.md
    HookPatterns.md
    AntDesignPatterns.md
  copilot-instructions.md
  README.md
```
