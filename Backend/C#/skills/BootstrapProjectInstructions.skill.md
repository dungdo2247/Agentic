---
name: Bootstrap Project Instructions
description: Scans the current project codebase, detects architecture pattern (CQRS, MVC, Vertical Slices, etc.), adapts generic instruction files to match, and generates project-specific .Project.Instructions.md files. Run as part of Tier 2 setup or standalone.
---

# Bootstrap Project Instructions

## When to use

- Automatically invoked as part of the Tier 2 setup prompt from the README
- Or run standalone after pulling stack guidance into a project

---

## Workflow

### Step 1 — Scan the project codebase

Explore the project's source code to detect:

| What to detect                 | How to detect                                                                                                                                                    |
| ------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Architecture pattern**       | Look for MediatR/CQRS handlers → Clean Architecture + CQRS. Controllers with service injection → MVC. Feature folders with handler + endpoint → Vertical Slices. |
| **Project structure**          | Identify solution file (.sln/.slnx), project files (.csproj), folder layout                                                                                      |
| **Domain layer**               | Entities, enums, value objects, aggregate roots, relationships                                                                                                   |
| **Application/Business layer** | Services, handlers, DTOs, validators, interfaces — adapt to whatever pattern is found                                                                            |
| **Infrastructure layer**       | DbContext, repositories, external service adapters, caching, background jobs                                                                                     |
| **API layer**                  | Controllers (MVC) or Minimal API endpoints, middleware, auth setup                                                                                               |
| **Settings**                   | appsettings.json structure, options classes, connection strings                                                                                                  |
| **Testing**                    | Test projects, frameworks used, shared fixtures                                                                                                                  |
| **Tech stack**                 | Database provider, ORM, auth scheme, job runner, file storage                                                                                                    |

### Step 2 — Fill in copilot-instructions.md

Update `.github/copilot-instructions.md` Project Guidelines with detected values:

```markdown
- Language/runtime: .NET {detected version}
- Database: {detected database}
- Architecture: {detected pattern}
- Background jobs: {detected job runner or "none"}
- Auth scheme: {detected auth scheme}
- File storage: {detected storage or "none"}
```

### Step 3 — Adapt generic instruction files

The generic instruction files from the repo are written as **defaults**. Adapt their **content** to match the detected architecture:

| If detected               | Adapt these instructions                                                                                                                                                                                                                               |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **MVC pattern**           | `Application.instructions.md` → describe Controllers, Services, ViewModels instead of CQRS. `WebApi.instructions.md` → describe Controller routing instead of Minimal API. `Naming.instructions.md` → adjust naming to Controller/Service conventions. |
| **CQRS + MediatR**        | Keep default content (already CQRS-oriented)                                                                                                                                                                                                           |
| **Vertical Slices**       | `Application.instructions.md` → describe feature folders with co-located handler + endpoint + validator                                                                                                                                                |
| **Minimal API (no CQRS)** | `Application.instructions.md` → describe service layer. `WebApi.instructions.md` → keep Minimal API content                                                                                                                                            |

Rules for adapting:

- **Keep the same file names and YAML frontmatter structure**
- **Rewrite the body content** to match the project's actual architecture
- **Keep the Related Pattern Files section** — update pattern references if patterns were also adapted
- Do not invent rules that contradict what the codebase actually does
- If something is unclear, keep the generic version and add a TODO comment

### Step 4 — Generate .Project.Instructions.md files

Create project-specific files in `.github/instructions/` with content from the scan.

**Always generate these (for any backend architecture):**

| File                                     | Content                                                                          |
| ---------------------------------------- | -------------------------------------------------------------------------------- |
| `Domain.Project.Instructions.md`         | Entities/models, enums, relationships, business rules discovered in the codebase |
| `Infrastructure.Project.Instructions.md` | Database provider, connection config, external services, caching, jobs, storage  |
| `Settings.Project.Instructions.md`       | appsettings.json sections, options classes, environment-specific values          |
| `Testing.Project.Instructions.md`        | Test projects, frameworks, shared fixtures, key test scenarios                   |

**Generate based on detected architecture:**

| If MVC                 | Generate                                                                                |
| ---------------------- | --------------------------------------------------------------------------------------- |
| Controllers + Services | `Application.Project.Instructions.md` — list services, DTOs, controller-service mapping |
| Controllers            | `WebApi.Project.Instructions.md` — list controllers, routes, action methods             |

| If CQRS            | Generate                                                                           |
| ------------------ | ---------------------------------------------------------------------------------- |
| Commands + Queries | `Application.Project.Instructions.md` — list features, commands, queries, handlers |
| Endpoints          | `WebApi.Project.Instructions.md` — list endpoint groups, routes                    |

Each `.Project.Instructions.md` file must:

- Start with YAML frontmatter: `name`, `description`
- Include ⚠️ notice that it's project-specific
- Reference the related generic instruction file
- Contain ONLY real data from the codebase — no leftover `{placeholder}` values
- Omit sections that don't apply to this project

### Step 5 — Report

Show a summary:

```
✅ Architecture detected: {pattern}
✅ copilot-instructions.md updated
✅ Generic instructions adapted: {list of modified files}
✅ Project-specific files generated: {list of .Project. files}
⚠️ Could not auto-detect: {list of items needing manual review}
```
