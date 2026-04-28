---
description: Initialize project documentation hub — analyze codebase, create docs structure, generate setup guide and initial review. Run ONCE per project.
---

# INIT PROJECT — Project Documentation Setup

## 🌐 Project Override Hook

This is a **global workflow**. Before executing the steps below, check whether the host project provides an override for it.

**Where to look** — in the host project's workspace root (NOT in the global workflows repo):

```
.windsurf/overrides/workflows/<same-filename-as-this-file>.md
```

Use the same filename as this workflow file's basename.

**If that override file exists**, read it and apply the first matching mode (declared in frontmatter as `mode: replace|extend|modify`, OR via a first-heading marker `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — IGNORE the steps below; execute ONLY the override file's instructions. The global workflow is fully shadowed.
- **EXTEND** — execute the steps below as written, then execute the override as an additional phase (or at the position the override specifies, e.g. "after Step 3").
- **MODIFY** — execute the steps below, but substitute / insert the steps the override declares (typically by step number or step title).

**If no override file exists**, ignore this section and proceed with the steps below as written.

If the override file's mode is missing or ambiguous, **STOP and ask the user** which mode applies before proceeding.

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

Onboard the AI development system onto this project. Analyzes the codebase, creates the `docs/` hub, generates LOCAL_SETUP.md, and produces an initial code quality report.

**Run ONCE per project. If `docs/` already has content, merge — don't overwrite.**

## Docs Structure (after init)

```
docs/
├── README.md              ← project hub, links all topics
├── CHANGELOG.md           ← root changelog, mirrors all topic entries
├── LOCAL_SETUP.md         ← how to run locally
├── research/
│   └── initial-project-review.md
└── {topic-slug}/          ← one folder per topic (created via /topic-new)
    ├── README.md
    ├── CHANGELOG.md
    ├── specs/
    ├── tickets/
    └── reports/
```

## Steps

### 1. Analyze Project Structure

// turbo
Read and analyze the project:

**Identify:**
- Tech stack: frameworks, languages, package managers
- Architecture: monorepo, multi-repo, monolith
- Key configuration files (angular.json, package.json, Dockerfile, etc.)
- Build and test commands
- CI/CD if present
- Existing documentation under `docs/`

**For existing projects:**
- Read all README files
- Check existing documentation topics
- Identify coding patterns and conventions
- Map the major modules/components

### 2. Create/Update Documentation Hub

If `docs/README.md` already exists, **merge** — don't overwrite. Add missing sections.
If it doesn't exist, create it.

**`docs/README.md`:**
```markdown
# {PROJECT_NAME} — Documentation Hub

## Overview
{Brief project description based on analysis}

**Tech Stack:** {detected stack}
**Repository:** {repo path}

## Quick Links

| Document | Description |
|----------|-------------|
| [LOCAL_SETUP.md](./LOCAL_SETUP.md) | Local development setup |
| [AI Workflows](../.windsurf/WORKFLOWS.md) | How AI-assisted development works |

## Topics

Each topic is a self-contained work area with its own tickets, reports, and changelog.

| Topic | Description | Status |
|-------|-------------|--------|
| [{topic-name}]({topic-slug}/) | {description} | {status} |

_Create new topics with `/topic-new`._
```

Register all existing topic folders (e.g., `app-update-v21/`) in the Topics table.

### 3. Create Root CHANGELOG.md

If `docs/CHANGELOG.md` does not exist, create it using the template from `.windsurf/_templates/changelog-template.md`:

```markdown
# Changelog — {PROJECT_NAME}

All notable changes for this project are documented here.
Each entry references the ticket spec, completion report, and git commit(s).
Entries are mirrored from topic changelogs, with a topic reference added.

---
```

If it already exists, leave it as-is.

### 4. Generate Local Setup

Execute `/_generate-local-setup` workflow to create `docs/LOCAL_SETUP.md`:
- Prerequisites (Node.js, npm, Angular CLI)
- Environment variables
- Build & run commands
- Test commands
- Ports & URLs
- Troubleshooting

### 5. Code Quality Review

Perform a focused review of the codebase:

**Architecture:**
- Project structure and organization
- Module separation
- Dependency management
- Configuration patterns

**Code Quality:**
- Naming conventions consistency
- Error handling patterns
- Type safety usage
- Dead code or unused imports
- Test coverage (presence/absence)

**Security:**
- Secrets handling (hardcoded vs. config)
- Authentication/authorization patterns

**Performance:**
- Bundle size concerns
- Lazy loading usage
- Obvious performance issues

### 6. Create Initial Report

Write `docs/research/initial-project-review.md`:

```markdown
# Initial Project Review — {PROJECT_NAME}

**Date:** {YYYY-MM-DD}
**Reviewer:** AI Assistant (Cascade)

## Project Overview
{Brief description of what the project does}

## Tech Stack
| Layer | Technology | Version |
|-------|-----------|---------|
| Frontend | {framework} | {version} |
| Build | {tool} | {version} |
| Test | {framework} | {version} |
| CI/CD | {platform} | — |

## Architecture Assessment
**Rating:** {Good / Adequate / Needs Improvement}
{2-3 paragraphs on architecture quality}

## Code Quality Assessment
**Rating:** {Good / Adequate / Needs Improvement}
{2-3 paragraphs on code quality}

## Key Findings
1. **{Finding}** — {impact and recommendation}

## Immediate Concerns
{List any critical issues}

## Recommendations
{Prioritized list of improvements}
```

### 7. Report

```
✅ Project Initialized: {PROJECT_NAME}

📁 Documentation Created/Updated:
- docs/README.md — project hub
- docs/CHANGELOG.md — root changelog
- docs/LOCAL_SETUP.md — development setup
- docs/research/initial-project-review.md — code review

📊 Project Analysis:
- Tech Stack: {summary}
- Architecture: {rating}
- Code Quality: {rating}
- Key Findings: {count}
- Existing Topics: {count}

🎯 Next Steps:
1. Review LOCAL_SETUP.md and verify you can run the project
2. Review initial-project-review.md findings
3. Start working on a topic: /kickoff or /topic-new
```

---

## Error Handling

| Error | Action |
|-------|--------|
| docs/ folder already has content | Merge — read existing, add missing sections, register existing topics |
| No recognizable project structure | Ask user for tech stack hint |
| _generate-local-setup fails | Create minimal LOCAL_SETUP.md manually |

## Notes

- Run ONCE per project — creates the documentation infrastructure
- Existing topic folders are preserved and registered in the hub
- The code quality review is intentionally brief — deep investigation happens per-topic
- This does NOT create topics — use `/topic-new` or `/kickoff` after
