---
description: Instructions how to initiate documentation and daily work flow on this new project
---

# INIT NEW PROJECT

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

Onboard the AI development system onto a new or existing project. Analyzes the codebase, creates the documentation structure, generates LOCAL_SETUP.md, and produces an initial code quality report.

**Protocol: This is run ONCE per project. It creates the entire docs/ infrastructure.**

## Required Inputs

- **PROJECT_NAME**: Human-readable project name
- **PROJECT_TYPE**: `existing` (joining existing codebase) or `new` (greenfield)

## Optional Inputs

- **CLIENT_NAME**: Client or organization name
- **REPO_URLS**: GitHub/Bitbucket repo URLs
- **TECH_STACK_HINT**: e.g., "Angular + .NET + SQL Server"

## Steps

### 1. Run Session Start

// turbo
Execute `/session-start` to gather initial context:
- Identify repo structure, branches, recent work
- Map workspace layout
- Check for existing documentation

### 2. Analyze Project Structure

// turbo
Read and analyze the entire project structure:

**Identify:**
- Repos and their purposes (FE, BE, docs, etc.)
- Tech stack: frameworks, languages, package managers
- Architecture pattern: monorepo, multi-repo, monolith, microservices
- Database: type, ORM, migration tool
- CI/CD: pipelines, deployment targets
- Key configuration files

**For existing projects:**
- Read README files in each repo
- Check existing documentation
- Identify coding patterns and conventions
- Map the major components/modules

### 3. Create Documentation Structure

// turbo
Create the docs infrastructure:

**`docs/README.md`:**
```markdown
# {PROJECT_NAME} — Documentation Hub

## Overview
{Brief project description based on analysis}

**Tech Stack:** {detected stack}
**Repos:**
- FE: {path}
- BE: {path}

## Quick Links
| Document | Description |
|----------|-------------|
| [WORK_LOG.md](./WORK_LOG.md) | Active work tracking |
| [CHANGELOG.md](./CHANGELOG.md) | Root change history |
| [LOCAL_SETUP.md](./LOCAL_SETUP.md) | Local development setup |

## Topics
_Topics will be added as work begins._

## Milestones
_Milestones will be tracked in UPWORK/ folder._
```

**`docs/WORK_LOG.md`:**
```markdown
# Work Log — {PROJECT_NAME}

## Active Milestone
_No active milestone yet. Run `/milestone-setup` to begin._

## Completed Milestones
_None yet._
```

**`docs/CHANGELOG.md`:**
```markdown
# Changelog — {PROJECT_NAME}

## {YYYY-MM-DD}
- Project documentation initialized
- Local setup guide generated
- Initial code review completed
```

**`docs/UPWORK/`** — create empty folder for future milestones

### 4. Generate Local Setup

Execute `/_generate-local-setup` workflow to create `docs/LOCAL_SETUP.md`:
- Prerequisites (SDKs, CLIs, package managers)
- Environment variables
- Service setup (DB, cache, storage)
- Build & run commands
- Migrations & seed data
- Ports & URLs
- Troubleshooting

### 5. Code Quality Review

Perform a focused review of the codebase. Check:

**Architecture:**
- Project structure clarity and organization
- Separation of concerns
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
- Input validation

**Performance:**
- Obvious N+1 queries or performance issues
- Caching strategy
- Bundle size concerns (FE)

### 6. Create Initial Report

Write `docs/RESEARCH/initial-project-review.md`:

```markdown
# Initial Project Review — {PROJECT_NAME}
[← Back to README](../README.md)

**Date:** {YYYY-MM-DD}
**Reviewer:** AI Assistant (Cascade)

## Project Overview
{Brief description of what the project does}

## Tech Stack
| Layer | Technology | Version |
|-------|-----------|---------|
| Frontend | {framework} | {version} |
| Backend | {framework} | {version} |
| Database | {type} | {version} |
| CI/CD | {platform} | — |

## Architecture Assessment
**Rating:** {Good / Adequate / Needs Improvement}
{2-3 paragraphs on architecture quality}

## Code Quality Assessment
**Rating:** {Good / Adequate / Needs Improvement}
{2-3 paragraphs on code quality}

## Key Findings
1. **{Finding}** — {impact and recommendation}
2. **{Finding}** — {impact and recommendation}

## Immediate Concerns
{List any critical issues that should be addressed}

## Recommendations
{Prioritized list of improvements}
```

### 7. Report

```
✅ Project Initialized: {PROJECT_NAME}

📁 Documentation Created:
- docs/README.md — project hub
- docs/WORK_LOG.md — work tracking
- docs/CHANGELOG.md — change history
- docs/LOCAL_SETUP.md — development setup
- docs/RESEARCH/initial-project-review.md — code review

📊 Project Analysis:
- Tech Stack: {summary}
- Architecture: {rating}
- Code Quality: {rating}
- Key Findings: {count}

🎯 Next Steps:
1. Review LOCAL_SETUP.md and verify you can run the project
2. Review initial-project-review.md findings
3. Get first milestone tasks: /milestone-setup {NUMBER}
4. Or create a topic directly: /topic-new
```

---

## Error Handling

| Error | Action |
|-------|--------|
| docs/ folder already exists | Read existing, ask: reset / merge / skip |
| No recognizable project structure | Ask user for PROJECT_TYPE and TECH_STACK_HINT |
| Can't detect tech stack | Ask user to provide TECH_STACK_HINT |
| _generate-local-setup fails | Create minimal LOCAL_SETUP.md manually |

## Notes

- Run this ONCE per project — it creates the infrastructure
- For existing projects with docs already, this can be run in "merge" mode
- The code quality review is intentionally brief — deep investigation happens in `/phase-research`
- This workflow does NOT create milestones or topics — use `/milestone-setup` or `/topic-new` after
