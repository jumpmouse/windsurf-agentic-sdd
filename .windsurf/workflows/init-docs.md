---
description: Bootstrap the docs/ folder at the user-specified destination (or project root if none provided), with WORK_LOG.md and README.md scaffolds. Run ONCE per project.
---

# /init-docs — Bootstrap the docs/ Folder

Lightweight bootstrap. Creates `docs/` (at the destination the user specifies, or at project root by default), with `README.md` (topic hub) and `WORK_LOG.md` (session-based root log). Does NOT create any topic — that's `/topic-new`.

## Inputs

- **DEST** (optional) — absolute or relative path where `docs/` should live. Default: project root.
- **PROJECT_NAME** (optional) — display name for the docs hub. Default: derive from `package.json`'s `name`, or the project root folder name.

## Steps

### 1. Resolve destination

- If `DEST` provided, use `{DEST}/docs/`.
- Otherwise, use `{PROJECT_ROOT}/docs/`.

If `docs/` already exists at the resolved path, **STOP** and report — do NOT overwrite. If the user wants to merge / refresh, they can run targeted updates manually.

### 2. Create folder

Create the resolved `docs/` folder.

### 3. Create README.md (docs hub)

`{DEST}/docs/README.md`:

```markdown
# {PROJECT_NAME} — Documentation Hub

## Overview

{One-line project description; user can edit later.}

## Quick Links

| Document | Description |
|----------|-------------|
| [WORK_LOG.md](./WORK_LOG.md) | Session-based root log — newest entries on top |
| [AGENTS.md](../AGENTS.md) | Project profile for AI agents (build / test / conventions) |

## Topics

Each topic is a self-contained work area with its own tickets, reports, and changelog.

| Topic | Description | Status |
|-------|-------------|--------|
| _Topics will be registered by `/topic-new`._ | — | — |
```

### 4. Create WORK_LOG.md

`{DEST}/docs/WORK_LOG.md`:

```markdown
# WORK_LOG — {PROJECT_NAME}

Session-based root log. Newest entries on top. Each topic gets a top-level section; sessions go under each topic's `## Sessions` subheading.

See `.windsurf/_templates/work-log-entry-template.md` for the entry format.

---

_Topic sections will be added by `/topic-new` as topics are created._
```

### 5. Report

```
✅ docs/ Initialized

📁 Created:
- {DEST}/docs/README.md      ← topic hub
- {DEST}/docs/WORK_LOG.md    ← session-based root log

🎯 Next:
- If AGENTS.md doesn't exist yet: /generate-local-setup (creates project profile + canonical Build / Test commands).
- Create your first topic: /topic-new
```

Then **STOP**.

## Notes

- This workflow is intentionally minimal. It does NOT detect tech stack (that's `/generate-local-setup`'s job for `AGENTS.md`).
- Per-topic structure is created by `/topic-new`, not here.
- `WORK_LOG.md` is the single root log — there is no separate root `CHANGELOG.md` (each topic keeps its own).
