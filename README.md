# Agentic — AI Guidance Repository

GitHub Copilot guidance organised by technology stack.
Uses a **two-tier model**: agent orchestrators live in your VS Code user space;
stack-specific guidance is pulled into each project on demand.

| Tier                         | What lives here                | Scope                          |
| ---------------------------- | ------------------------------ | ------------------------------ |
| **User space** `~/.github/`  | Agent files + root entry point | All workspaces, installed once |
| **Project space** `.github/` | Instructions, skills, patterns | Per repo, pulled on demand     |

## Stacks

| Stack                          | Folder              | Guide                                         |
| ------------------------------ | ------------------- | --------------------------------------------- |
| **C# / .NET**                  | `Backend/C#/`       | [Backend README](Backend/C%23/README.md)      |
| **Vue + Tailwind**             | `Frontend/Vue/`     | [Frontend README](Frontend/Vue/README.md)     |
| **ReactJS + Umi + Ant Design** | `Frontend/ReactJs/` | [Frontend README](Frontend/ReactJs/README.md) |

## Repository layout

```
Backend/
  C#/
    agents/          <- agent file (copied to user space)
    instructions/    <- generic rules, adapted per project during setup
    skills/          <- task workflows
    patterns/        <- implementation references
    copilot-instructions.md
Frontend/
  Vue/
    agents/
    instructions/
    skills/
    patterns/
    copilot-instructions.md
  ReactJs/
    agents/
    instructions/
    skills/
    patterns/
    copilot-instructions.md
README.md
```

---

## How to set up

Each stack's README has a **single prompt** you copy and paste into any AI chat.
The AI will:

1. Clone this repo
2. Copy generic guidance files into your project's `.github/`
3. **Scan your project's codebase** to detect architecture, tech stack, and patterns
4. **Adapt** the generic instruction files to match your project (e.g. MVC vs CQRS, Tailwind vs SCSS)
5. **Generate** `.Project.Instructions.md` files with content from YOUR codebase
6. Clean up

No manual placeholder filling needed — everything is auto-detected.

### Stack READMEs

- **Backend (C# / .NET)** → [`Backend/C#/README.md`](Backend/C%23/README.md)
- **Frontend (Vue)** → [`Frontend/Vue/README.md`](Frontend/Vue/README.md)
- **Frontend (ReactJS)** → [`Frontend/ReactJs/README.md`](Frontend/ReactJs/README.md)

### What gets generated in your project

```
{workspaceRoot}/
  .github/
    copilot-instructions.md          ← auto-filled with your tech stack
    instructions/
      *.instructions.md              ← generic rules, adapted to your architecture
      *.Project.Instructions.md      ← generated from your codebase
    skills/
    patterns/
```

| File type                   | In this repo             | In your project                   |
| --------------------------- | ------------------------ | --------------------------------- |
| `*.instructions.md`         | Default/template content | Adapted to your architecture      |
| `*.Project.Instructions.md` | **Not included**         | Auto-generated from your codebase |
| `copilot-instructions.md`   | Placeholder template     | Auto-filled with detected values  |

---

## Precedence inside any stack

```
Instructions  >  Skills  >  Patterns
```
