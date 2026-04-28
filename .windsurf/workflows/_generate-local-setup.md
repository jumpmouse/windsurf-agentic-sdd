---
description: Investigate a web project and generate a comprehensive LOCAL_SETUP.md
---


## 🌐 Project Override Hook

Before executing this workflow, check whether the host project provides an override.

**Path** (in the host project's workspace root, NOT this global repo):
`.windsurf/overrides/workflows/<same-filename-as-this-file>.md`

If present, apply its mode (frontmatter `mode:` OR first heading `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — run ONLY the override; ignore the steps below.
- **EXTEND** — run the steps below as written, then run the override as a final phase (or at the position it specifies).
- **MODIFY** — run the steps below, with the steps the override declares substituted / inserted at the matching positions.

No override → proceed as written. Ambiguous mode → **STOP and ask the user**.

---

> ⚠️ **MANDATORY EXECUTION PROTOCOL**
> This is a **strict step-by-step workflow**, NOT background context.
> - Execute steps **in order**, one at a time
> - **Do NOT skip steps** — every step exists for a reason
> - **Do NOT combine or optimize** steps unless explicitly allowed
> - **Do NOT treat this as context** to inform freeform work
> - After completing each step, move to the next step
> - If a step says to execute another workflow, **execute it fully** before continuing with this one
> - If you finished all steps here AND this workflow is called from parent workflow, get back to executing parent workflow steps.
> - If you finished all steps AND there are no parent workflow, mandatory is to stop and report to the user. Do not jump to "logical" next step!
>
> **Violation of this protocol undermines the entire development system.**

Inputs (provide when running this workflow):

- PROJECT_ROOT: absolute path to the repository root (default: current workspace root)
- WEB_STACK_HINT: optional hints (e.g., dotnet+angular, nextjs, python+django), speeds up detection

1. Inventory the repository

- Map the top-level structure under `{PROJECT_ROOT}` (e.g., `be/`, `fe/`, `apps/`, `packages/`, `services/`, `docs/`).
- Identify monorepo managers: check for `nx.json`, `turbo.json`, `pnpm-workspace.yaml`, `lerna.json`, `workspace.json`.
- Identify package managers: `pnpm-lock.yaml`, `yarn.lock`, `package-lock.json`, `poetry.lock`, `Pipfile.lock`.
- Identify language ecosystem markers:
  - Node/TS: `package.json`, `tsconfig*.json`, `vite.config.*`, `next.config.*`, `angular.json`, `nuxt.config.*`, `svelte.config.*`, `astro.config.*`
  - .NET: `global.json`, `*.sln`, `*.csproj`, `appsettings*.json`
  - Java: `pom.xml`, `build.gradle*`, `gradlew`
  - Python: `pyproject.toml`, `Pipfile`, `requirements.txt`
  - Go: `go.mod`
  - PHP: `composer.json`
  - Ruby: `Gemfile`
- Containers/infra: `Dockerfile*`, `docker-compose*.yml`, `compose.yml`, `Makefile`, `Procfile`, `k8s/`, `.devcontainer/`.
- CI/CD: `.github/workflows/*.yml`, `.gitlab-ci.yml`, `azure-pipelines.yml`.

2. Detect runtimes and versions

- Node.js: read `.nvmrc` or `.node-version`, else check `engines` in `package.json`.
- .NET: read `global.json` (SDK), else infer from `*.csproj` `TargetFramework`.
- Java: Gradle wrapper or Maven toolchain; note JDK version.
- Python: Python version from `pyproject.toml` or `runtime.txt`.
- Document required package manager (pnpm/yarn/npm) and version if defined.

3. Catalog applications and entrypoints

- Backend(s): list each service/app, framework (e.g., .NET Web API, Express, Django), main project file (`*.csproj`, `main.ts`, `Program.cs`, `manage.py`), primary start command.
- Frontend(s): framework (Angular/React/Next/Nuxt/Svelte/Astro), app name, start/build commands.
- Shared libraries/packages: note build steps if required.

4. Commands (build, run, test, lint, format)

- Node/monorepo: read `package.json` scripts (root and per-app). For Nx, map `nx.json` targets (`serve`, `build`, `test`, `lint`, `e2e`).
- .NET: `dotnet restore/build/test`; migrations if EF Core.
- Python: venv/poetry steps, `pytest`, linters (`ruff`, `flake8`), formatters (`black`).
- Java: `mvn`/`gradle` tasks.
- Add canonical commands to LOCAL_SETUP with exact invocations and working directories.

5. Environment variables and secrets

- Gather from: `.env*`, `appsettings*.json`, `settings.py`, `config/*.ts|js`, CI secrets references, README notes.
- Produce a complete list of required env vars: names, purpose, example values, and whether required/optional.
- Create or update `.env.example` (do not include real secrets). Reference it in LOCAL_SETUP.

6. Datastores and emulators

- List all datastores used (SQL Server/Postgres/MySQL/SQLite/Mongo/Redis/Elasticsearch/etc.).
- Capture connection strings and migration tools (EF Core/Prisma/TypeORM/Sequelize/Hibernate/Django ORM/etc.).
- Include setup steps: Docker services, local installs, or cloud emulators (Azurite/LocalStack/MinIO/Mailhog/RabbitMQ/Redis). Provide exact commands and default ports.
- Document how to run migrations and seed data. Include make/Nx targets if present.

7. Networking and runtime

- Ports and URLs for each service/app (dev and test). Note proxies/reverse proxies (e.g., Nginx) and CORS settings.
- AuthN/AuthZ notes: local accounts, seed users, roles, login flows.
- Asset/storage policies (e.g., public vs private containers, media formats, file size limits).

8. CI/CD and quality gates

- Summarize CI workflows (build, test, lint, release, deploy) and how to run similar steps locally.
- List minimum quality gates (tests pass, lints clean, format, typecheck) with commands.

9. Troubleshooting

- Common pitfalls: missing SDKs, port conflicts, SSL cert issues, migrations failing, puppeteer/playwright deps.
- Reset procedures: clean caches, remove containers/volumes, reset DB, clear node_modules.

10. Generate `docs/LOCAL_SETUP.md`

- Create a complete document with:
  - Prerequisites (SDKs, CLIs, package managers)
  - Project layout overview
  - Environment variables (.env.example) and secrets guidance
  - Service setup (DB/Cache/Storage/MQ) and emulators
  - Build & run (per app/service) with exact commands
  - Tests, linting, formatting, typecheck
  - Migrations & seed data
  - Accounts and roles (sample creds or how to create)
  - Ports and URLs (dev/test)
  - CI parity notes
  - Troubleshooting

Template for LOCAL_SETUP.md (copy and fill):

```
# Local Setup

## Prerequisites
- Node.js: vX.Y.Z (manager: pnpm/yarn/npm)
- .NET SDK: X.Y.Z
- JDK: X if applicable
- Docker & Compose: versions
- Other CLIs: ...

## Project layout
- be/: ...
- fe/: ...

## Environment
- Copy `.env.example` to `.env` and fill:
  - VAR_A: purpose
  - VAR_B: purpose

## Services
- Start DB/Cache/Storage:
  - docker compose up -d <services>
- Migrations: ...
- Seed: ...

## Build & Run
- Backend: command + cwd
- Frontend: command + cwd

## Quality
- Tests: ...
- Lint/Format: ...

## Accounts & Roles
- Admin: ...

## Ports & URLs
- API: http://localhost:xxxx
- FE: http://localhost:yyyy

## Troubleshooting
- Tip 1: ...
```
