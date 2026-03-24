# Backend Guidance C# / .NET

AI guidance for C# / .NET projects using GitHub Copilot.

Stack: **C#**, **.NET**, **Entity Framework Core**

---

## What is this?

This folder provides structured guidance that GitHub Copilot uses to assist with backend tasks.
It follows a **two-tier model**:

| Tier              | Content                                 | Where it lives                                                         | When                         |
| ----------------- | --------------------------------------- | ---------------------------------------------------------------------- | ---------------------------- |
| **User space**    | `Backend.agent.md` (orchestrator)       | `~/.github/agents/`                                                    | Installed once per machine   |
| **Project space** | `instructions/`, `skills/`, `patterns/` | `{repo}/.github/instructions/`, `.github/skills/`, `.github/patterns/` | Pulled per project on demand |

---

## Tier 1 — Install the agent to user space (one-time)

This makes the Backend agent available in **every VS Code workspace** on this machine.

Copy the prompt below and paste it into any AI chat (Copilot, ChatGPT, Claude, etc.).
The AI will clone the repo, copy the agent file, and clean up.

```
I want to set up AI guidance from {public_github} into my VS Code user space so it applies to all my workspaces.

Please do the following steps for me:

1. Clone the repo with a shallow clone (--depth 1) into a temporary directory.

2. Detect my OS:
   - Windows: user space is %USERPROFILE%\.github\
   - Linux / macOS: user space is ~/.github/

3. Create the following folder in user space if it does not exist:
   - agents/

4. Copy these files from the cloned repo to user space:
   - Backend/C#/agents/Backend.agent.md          ->  {userSpace}/agents/Backend.agent.md
   - Frontend/Vue/agents/Frontend.agent.md       ->  {userSpace}/agents/Frontend-vue.agent.md
   - Frontend/ReactJs/agents/Frontend.agent.md   ->  {userSpace}/agents/Frontend-reactjs.agent.md

5. Delete the temporary clone directory completely.

6. Show me the final file tree under my user space .github/ folder so I can confirm everything is in place.
```

### Result in user space

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

Run this inside the specific project where you want full C# context.
Copy the prompt below and paste it into any AI chat.

The AI will clone the repo, copy generic guidance files, **scan your project's codebase**,
detect the architecture pattern, adapt instruction content, and generate project-specific
`.Project.Instructions.md` files — all automatically.

```
I want to set up C# / .NET AI guidance for this project from {public_github}.

Please do the following steps for me:

### Step 1 — Clone and copy

1. Clone the repo with a shallow clone (--depth 1) into a temporary directory.

2. Create the following folders in the current workspace root if they do not exist:
   - .github/instructions/
   - .github/skills/
   - .github/patterns/

3. Copy these files from the cloned repo (do NOT copy any *.Project.Instructions.md files):
   - Backend/C#/instructions/*.instructions.md  ->  .github/instructions/
   - Backend/C#/skills/*                        ->  .github/skills/
   - Backend/C#/patterns/*                      ->  .github/patterns/
   - Backend/C#/copilot-instructions.md         ->  .github/copilot-instructions.md

4. Delete the temporary clone directory completely.

### Step 2 — Scan my codebase

Now analyze my project's source code to detect:

a. **Architecture pattern** — look at the project structure and code:
   - MediatR handlers / CQRS command+query separation → "Clean Architecture + CQRS"
   - Controllers with injected services / repository pattern → "MVC"
   - Feature folders with co-located handlers, models, validators → "Vertical Slices"
   - Minimal API with endpoint classes → "Minimal API"
   - Other → describe what you find

b. **Tech stack** — scan .csproj files, Program.cs, appsettings.json, usings:
   - .NET version
   - Database and ORM (EF Core, Dapper, etc.)
   - Auth scheme (JWT, Cookie, Identity, etc.)
   - Background jobs (Hangfire, Quartz, hosted services, etc.)
   - File storage (Azure Blob, AWS S3, local, Cloudinary, etc.)
   - Logging (Serilog, NLog, default, etc.)
   - Validation (FluentValidation, DataAnnotations, etc.)
   - Any other notable libraries

### Step 3 — Fill in copilot-instructions.md

Open .github/copilot-instructions.md and replace all `{placeholder}` values in the
"Project Guidelines" section with the actual values detected in Step 2.

### Step 4 — Adapt generic instruction files

Review each .github/instructions/*.instructions.md file and adapt its content
to match my project's actual architecture from Step 2.

Key adaptations:
- If my project uses **MVC** (not CQRS): rewrite Application.instructions.md to describe
  Controllers → Services → Repositories flow instead of MediatR commands/queries.
- If my project uses **Minimal API**: rewrite WebApi.instructions.md for endpoint classes
  instead of controller-based routing.
- If my project uses **Vertical Slices**: rewrite both Application and WebApi instructions
  to describe feature folders with co-located files.
- Adapt Infrastructure.instructions.md to match my actual ORM, external services, and patterns.
- Adapt Testing.instructions.md to match my actual test framework and conventions.
- Adapt Settings.instructions.md to match my actual configuration pattern.

Do NOT change Architecture.instructions.md or Naming.instructions.md unless the project
clearly uses different conventions.

### Step 5 — Generate .Project.Instructions.md files

Scan my actual source code and generate these files in .github/instructions/:

- **Domain.Project.Instructions.md** — list all entities, enums, value objects, and their
  relationships as found in my domain/model layer.
- **Application.Project.Instructions.md** — list all services/handlers, DTOs, request/response
  models, and interfaces as found in my application/business logic layer.
- **Infrastructure.Project.Instructions.md** — list all repositories, DbContext configuration,
  external service integrations, job definitions, and caching as found in my infrastructure layer.
- **WebApi.Project.Instructions.md** — list all controllers/endpoints, route conventions,
  middleware, and filters as found in my API layer.
- **Settings.Project.Instructions.md** — list all appsettings sections, options/configuration
  classes, and environment-specific settings.
- **Testing.Project.Instructions.md** — list all test projects, test base classes, fixtures,
  mocking patterns, and test categories.

Each file should contain actual names, namespaces, and patterns from MY codebase — not
generic placeholders.

### Step 6 — Summary

Show me a summary of:
- Detected architecture pattern
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
      Application.instructions.md    ← adapted to your architecture
      Architecture.instructions.md
      Domain.instructions.md
      Infrastructure.instructions.md ← adapted to your stack
      Naming.instructions.md
      Settings.instructions.md       ← adapted to your config pattern
      Testing.instructions.md        ← adapted to your test framework
      WebApi.instructions.md         ← adapted to your API style
      *.Project.Instructions.md      ← generated from your codebase
    skills/
      ...
    patterns/
      ...
```

---

## How to use

Once the agent (Tier 1) is in user space, Copilot is ready for backend tasks in any workspace.
Once the stack guidance (Tier 2) is pulled into a project, Copilot has full context for that repo.

Use natural language in Copilot Chat:

| Task                      | What to say                                                   |
| ------------------------- | ------------------------------------------------------------- |
| Create a new API endpoint | _"Create an endpoint for GET /products"_                      |
| Add a use case / command  | _"Create a use case to place an order"_                       |
| Update the domain model   | _"Add a Status property to the Order entity"_                 |
| Implement infrastructure  | _"Implement the IEmailSender with SendGrid"_                  |
| Review a change           | _"Review this change for architecture issues"_                |
| Write tests               | _"Write tests for the CreateOrderUseCase"_                    |
| Refactor a feature        | _"Refactor the payment feature to follow clean architecture"_ |

Copilot loads `Backend.agent.md`, selects the relevant skill, and applies the instructions and patterns.

---

## Re-generate project-specific instructions

If your project evolves (new entities, services, etc.), paste this into Copilot Chat:

```
Re-scan my codebase and update all .Project.Instructions.md files in .github/instructions/
to reflect the current state of the project.

For each .Project.Instructions.md file:
- Re-read the actual source code for that layer
- Update the file with current entities, services, endpoints, settings, etc.
- Keep the generic *.instructions.md files unchanged

Show me what changed in each file.
```

---

## Update guidance

Re-copy the Tier 2 prompt and run it again. It will overwrite with the latest version from the repo.

---

## Verify

1. Open VS Code in any workspace.
2. Open Copilot Chat.
3. Ask: _"What guidance do you have loaded?"_

Copilot should mention `Backend.agent.md`.
If stack guidance is pulled into the project, it should mention the instruction files too.

If it does not:

- Confirm `~/.github/copilot-instructions.md` exists.
- Confirm VS Code setting `github.copilot.chat.codeGeneration.useInstructionFiles` is `true`.
- Confirm VS Code **1.93+** is installed.
