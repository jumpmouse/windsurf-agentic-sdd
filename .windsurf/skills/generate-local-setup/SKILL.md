---
name: generate-local-setup
description: Walk the project, collect tech-stack / build / test / env info, and create a root-level AGENTS.md file that captures project context for AI agents. Establishes canonical {BUILD_COMMAND} and {TEST_COMMAND} anchors.
---

# Generate Local Setup Skill

## Persona

**MANDATORY first action:** Load the Investigator persona by reading `.windsurf/personas/investigator.md`. The Investigator's "no assumptions, evidence-grounded, end-to-end" discipline is exactly what's needed to produce an honest project profile.

## Inputs

- **PROJECT_ROOT** (optional) — absolute path to repository root. Default: current workspace root.
- **WEB_STACK_HINT** (optional) — hints (e.g. `dotnet+angular`, `nextjs`, `python+django`) to speed up detection.

## Output

`AGENTS.md` at the **project root** (NOT inside `docs/` and NOT inside `.windsurf/`). This file is the canonical place for AI agents (Cascade, Claude Code, etc.) to learn project conventions. It serves as both:

1. A human-readable project profile.
2. The source of truth for `{BUILD_COMMAND}` and `{TEST_COMMAND}` anchors that other workflows / skills resolve at runtime.

## Procedure

### 1. Inventory the repository

Map top-level structure under `{PROJECT_ROOT}` (e.g., `be/`, `fe/`, `apps/`, `packages/`, `services/`, `docs/`).

Identify:

- **Monorepo managers** — `nx.json`, `turbo.json`, `pnpm-workspace.yaml`, `lerna.json`, `workspace.json`.
- **Package managers** — `pnpm-lock.yaml`, `yarn.lock`, `package-lock.json`, `poetry.lock`, `Pipfile.lock`.
- **Language ecosystem markers**:
  - Node/TS: `package.json`, `tsconfig*.json`, `vite.config.*`, `next.config.*`, `angular.json`, `nuxt.config.*`, `svelte.config.*`, `astro.config.*`
  - .NET: `global.json`, `*.sln`, `*.csproj`, `appsettings*.json`
  - Java: `pom.xml`, `build.gradle*`, `gradlew`
  - Python: `pyproject.toml`, `Pipfile`, `requirements.txt`
  - Go: `go.mod`
  - PHP: `composer.json`
  - Ruby: `Gemfile`
- **Containers / infra** — `Dockerfile*`, `docker-compose*.yml`, `compose.yml`, `Makefile`, `Procfile`, `k8s/`, `.devcontainer/`.
- **CI / CD** — `.github/workflows/*.yml`, `.gitlab-ci.yml`, `azure-pipelines.yml`.

### 2. Detect runtimes and versions

- Node.js: read `.nvmrc` / `.node-version`; else check `engines` in `package.json`.
- .NET: read `global.json`; else infer from `*.csproj` `TargetFramework`.
- Java: Gradle wrapper or Maven toolchain; note JDK version.
- Python: from `pyproject.toml` or `runtime.txt`.
- Required package manager (pnpm/yarn/npm) and version if defined.

### 3. Catalog applications and entrypoints

- **Backend(s)**: each service / app, framework, main project file, primary start command.
- **Frontend(s)**: framework, app name, start / build commands.
- **Shared libraries / packages**: build steps if required.

### 4. Commands (build, run, test, lint, format)

- Node / monorepo: read `package.json` scripts (root + per-app). For Nx, map `nx.json` targets (`serve`, `build`, `test`, `lint`, `e2e`).
- .NET: `dotnet restore/build/test`; migrations if EF Core.
- Python: venv / poetry, `pytest`, linters (`ruff`, `flake8`), formatters (`black`).
- Java: `mvn` / `gradle` tasks.
- Capture canonical commands with exact invocations and working directories.

### 5. Environment variables and secrets

- Gather from `.env*`, `appsettings*.json`, `settings.py`, `config/*.ts|js`, CI secrets, README notes.
- Produce a complete list: names, purpose, example values, required/optional.
- Create or update `.env.example` (no real secrets). Reference in AGENTS.md.

### 6. Datastores and emulators

- List all datastores (SQL Server / Postgres / MySQL / SQLite / Mongo / Redis / Elasticsearch / etc.).
- Capture connection strings + migration tools (EF Core / Prisma / TypeORM / Sequelize / Hibernate / Django ORM).
- Setup steps: Docker services, local installs, cloud emulators (Azurite / LocalStack / MinIO / Mailhog / RabbitMQ / Redis). Exact commands + default ports.
- How to run migrations and seed data.

### 7. Networking and runtime

- Ports + URLs for each service / app (dev + test).
- Proxies / reverse proxies, CORS settings.
- AuthN/Z notes: local accounts, seed users, roles, login flows.
- Asset / storage policies.

### 8. CI/CD and quality gates

- Summarize CI workflows (build, test, lint, release, deploy) and how to run similar locally.
- Minimum quality gates (tests pass, lints clean, format, typecheck) with commands.

### 9. Troubleshooting

- Common pitfalls: missing SDKs, port conflicts, SSL cert issues, migrations failing, puppeteer/playwright deps.
- Reset procedures: clean caches, remove containers/volumes, reset DB, clear node_modules.

### 10. Generate AGENTS.md

Create `AGENTS.md` at project root using this shape:

```markdown
# AGENTS.md — {PROJECT_NAME}

> This file is the project profile for AI agents (Cascade, Claude Code, etc.) and human contributors. It is the source of truth for `{BUILD_COMMAND}` and `{TEST_COMMAND}` resolution.

## Project Overview
{2-3 sentences on what the project does + tech stack summary.}

## Tech Stack
- {layer}: {framework} {version}
- ...

## Project Layout
- `be/`: ...
- `fe/`: ...

## Prerequisites
- Node.js: vX.Y.Z (manager: pnpm/yarn/npm)
- .NET SDK: X.Y.Z
- Docker & Compose: versions
- Other CLIs: ...

## Environment
- Copy `.env.example` to `.env` and fill:
  - `VAR_A`: purpose
  - `VAR_B`: purpose

## Services
- Start DB / Cache / Storage:
  - `docker compose up -d <services>`
- Migrations: ...
- Seed: ...

## Build Command

```
{single canonical build command — e.g. `pnpm build`, `dotnet build`, `npx ng build`}
```

## Test Command

```
{single canonical test command — e.g. `pnpm test`, `dotnet test`, `npx ng test --watch=false`}
```

## Build & Run (verbose, multi-app)
- Backend: command + cwd
- Frontend: command + cwd

## Quality Gates
- Tests: ...
- Lint / Format: ...
- Typecheck: ...

## Accounts & Roles
- Admin: ...

## Ports & URLs
- API: http://localhost:xxxx
- Frontend: http://localhost:yyyy

## Conventions
- {Convention 1}: ...
- {Convention 2}: ...

## Troubleshooting
- {Tip 1}: ...
```

**Convention for `{BUILD_COMMAND}` and `{TEST_COMMAND}` resolution:**
Other workflows / skills that reference `{BUILD_COMMAND}` or `{TEST_COMMAND}` resolve them by reading the **first fenced code block** under the `## Build Command` and `## Test Command` headings respectively in `AGENTS.md`. These two headings MUST exist and MUST contain a single, copy-paste-ready command (no surrounding prose inside the code block). For multi-app projects, pick the most representative command (the one CI runs on every PR) — secondary commands go in the verbose "Build & Run" / "Quality" sections.

## Final Report

```
✅ AGENTS.md Generated — {PROJECT_NAME}

📁 File: {PROJECT_ROOT}/AGENTS.md

🔍 Project Profile:
- Tech stack: {summary}
- Build command: `{BUILD_COMMAND}`
- Test command: `{TEST_COMMAND}`
- Datastores: {list}
- Ports: {list}

🎯 Next Steps:
1. Review AGENTS.md for accuracy.
2. Adjust the canonical Build / Test commands if needed.
3. Run `/init-docs` to bootstrap the docs/ folder + WORK_LOG.md.
```

## Error Handling

| Error | Action |
|-------|--------|
| No recognizable project structure | Ask user for `WEB_STACK_HINT`. |
| `AGENTS.md` already exists | Merge — read existing, update sections, keep custom content. |
| Cannot detect a single canonical Build / Test command | Use the most representative; flag others in the verbose section; STOP for user confirmation. |
