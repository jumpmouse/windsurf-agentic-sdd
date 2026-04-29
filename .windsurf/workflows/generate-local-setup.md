---
description: Walk the project, collect tech-stack / build / test / env info, and create a root-level AGENTS.md file that captures project context for AI agents.
---

# /generate-local-setup — Generate Project AGENTS.md

Thin entry point. Loads the `generate-local-setup` skill (which loads `@Personas:investigator`) and executes its full procedure. Output: `AGENTS.md` at project root.

## Inputs

- **PROJECT_ROOT** (optional) — absolute path to repository root. Default: current workspace root.
- **WEB_STACK_HINT** (optional) — hints (e.g. `dotnet+angular`, `nextjs`, `python+django`) to speed up detection.

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/generate-local-setup/SKILL.md` end-to-end with the inputs above. The skill:

1. Loads `@Personas:investigator` (no-assumptions discipline for evidence-grounded project profiling).
2. Inventories the repository (top-level structure, monorepo managers, package managers, language ecosystem markers, containers / infra, CI/CD).
3. Detects runtimes and versions.
4. Catalogs applications + entrypoints.
5. Captures canonical commands (build, run, test, lint, format).
6. Documents environment variables, datastores, networking, CI gates, troubleshooting.
7. Generates `AGENTS.md` at project root with `## Build Command` and `## Test Command` headings as canonical anchors for `{BUILD_COMMAND}` / `{TEST_COMMAND}` resolution.

### 2. STOP

The skill handles its own STOP / report.

## After it finishes

- Review `AGENTS.md` for accuracy.
- Adjust the canonical Build / Test commands if needed.
- Run `/init-docs` to bootstrap the `docs/` folder + `WORK_LOG.md`.

## Notes

- Run **once per project** when onboarding the agentic-SDD system.
- If `AGENTS.md` already exists, the skill **merges** (does not overwrite).
- The `## Build Command` and `## Test Command` headings are load-bearing — workflows that reference `{BUILD_COMMAND}` / `{TEST_COMMAND}` resolve them by reading the first fenced code block under those headings.
