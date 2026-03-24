# Frontend AI Guidance

This folder contains AI guidance (agents, instructions, skills, patterns)
for frontend stacks.

## Stacks

| Stack                          | Folder     | Status                                              |
| ------------------------------ | ---------- | --------------------------------------------------- |
| **Vue + Tailwind**             | `Vue/`     | Available — see [Vue README](Vue/README.md)         |
| **ReactJS + Umi + Ant Design** | `ReactJs/` | Available — see [ReactJS README](ReactJs/README.md) |

## How it works

Each stack's README has a **single prompt** you copy into any AI chat.
The AI will:

1. Pull the generic guidance files into your project's `.github/`
2. **Scan your project's codebase** to detect the actual tech stack and patterns
3. **Adapt** the generic instruction files to match (e.g. Composition API vs Options API, Tailwind vs SCSS)
4. **Generate** `.Project.Instructions.md` files with content from YOUR codebase

No pre-built project-specific files exist in this repo — they are always generated fresh
from the target project's code.

## Adding a new stack

Create a sub-folder named after the stack (e.g. `Angular/`, `Svelte/`) with this structure:

```
Frontend/{Stack}/
  agents/
    Frontend.agent.md
  instructions/
    Architecture.instructions.md     ← generic rules (default content)
    Components.instructions.md
    Naming.instructions.md
    Styling.instructions.md
    Testing.instructions.md
  skills/
    CreateComponent.skill.md
    CreatePage.skill.md
    WriteTests.skill.md
    ...
  patterns/
    ...
  copilot-instructions.md
  README.md                          ← contains the setup prompt
```

After running the setup prompt, files in the target project look like:

```
{workspaceRoot}/.github/
  copilot-instructions.md            ← auto-filled
  instructions/
    *.instructions.md                ← adapted to the project
    *.Project.Instructions.md        ← generated from the codebase
  skills/
  patterns/
```
