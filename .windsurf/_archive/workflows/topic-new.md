---
description: Create a new docs Topic using the project conventions with intelligent discovery
---

# TOPIC NEW

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

Create a new documentation topic with complete folder scaffolding, intelligent discovery questions, and optional plan generation.

**Protocol: Every topic gets the same folder structure. No files are optional.**

## Required Inputs

- **TOPIC_TITLE**: Human-readable name (e.g., "Admin Assets Grid View")
- **FOLDER_SLUG**: Kebab-case (e.g., "admin-assets-grid")
- **HIGH_LEVEL_DESCRIPTION**: 2-4 paragraphs covering problem, feature, scope (BE/FE/DB), constraints

## Optional Inputs

- **LINEAR_ISSUES**: List of LAN-XXX IDs associated with this topic
- **MILESTONE_NUMBER**: Milestone this topic belongs to
- **BRANCH_NAME**: If branch already exists

## Steps

### 1. Validate Inputs

// turbo
- Confirm `docs/{FOLDER_SLUG}/` does NOT already exist (if it does, report and ask)
- If LINEAR_ISSUES provided, fetch details via `mcp2_get_issue` for each
- If MILESTONE_NUMBER provided, confirm milestone folder exists

### 2. Scaffold Topic Folder

// turbo
Create `docs/{FOLDER_SLUG}/` with ALL these files:

**README.md:**
```markdown
# {TOPIC_TITLE}

## Overview

{HIGH_LEVEL_DESCRIPTION}

**Linear Issues:** {LINEAR_ISSUES as links, or "N/A"}
**Branch:** `{BRANCH_NAME or "TBD"}`
**Milestone:** {MILESTONE_NUMBER or "N/A"}

---

## Quick Links

| Document | Description |
|----------|-------------|
| [PLAN.md](./PLAN.md) | Phases, tasks, progress tracking |
| [CHANGELOG.md](./CHANGELOG.md) | Detailed change history |
| [DISCOVERY.md](./DISCOVERY.md) | Requirements & questions |

---

## Current Status

- **Topic Status:** ⏳ Not Started
- **Active Phase:** Phase 0 — Discovery

---

## Back Links

- [← Root WORK_LOG](../WORK_LOG.md)
{if milestone: - [← Milestone {N}](../UPWORK/milestone%20{N}/README.md)}
```

**PLAN.md:**
```markdown
# Plan — {TOPIC_TITLE}
[← Back to README](./README.md)

## Overview

{HIGH_LEVEL_DESCRIPTION}

---

## Phases

### Phase 0 — Discovery & Requirements

**Status:** 🔄 In Progress
**Depends on:** —

- [x] Scaffold topic documentation
- [x] Generate discovery questions
- [ ] User provides answers to discovery questions
- [ ] Finalize plan with detailed phases and tasks

**Done when:** All discovery questions answered, detailed phases added below.

---

_Detailed implementation phases will be added after discovery._
```

**CHANGELOG.md:**
```markdown
# Changelog — {TOPIC_TITLE}
[← Back to README](./README.md)

## {YYYY-MM-DD}
- Topic scaffolded — discovery questions generated
- Awaiting requirements input to finalize plan
```

**DISCOVERY.md:**
```markdown
# Discovery & Requirements — {TOPIC_TITLE}
[← Back to README](./README.md)

## High-Level Description

{HIGH_LEVEL_DESCRIPTION}

## Analysis

### Components Involved
{AI-generated list based on description analysis}

### Estimated Complexity
{Low / Medium / High — with reasoning}

### Key Unknowns
{List of things that aren't clear from the description}

---

## Discovery Questions

{AI-generated questions — see Step 3}

---

## Answers

_User provides answers here or in conversation._
```

**SESSION-CHANGES-{YYYY-MM-DD}.md:**
```markdown
# Session Changes — {YYYY-MM-DD}
[← Back to README](./README.md)

## Files Changed

### docs
- `docs/{FOLDER_SLUG}/README.md` — created
- `docs/{FOLDER_SLUG}/PLAN.md` — created
- `docs/{FOLDER_SLUG}/CHANGELOG.md` — created
- `docs/{FOLDER_SLUG}/DISCOVERY.md` — created

## Context
- Phase: Phase 0 — Discovery
- Tasks completed: Topic scaffolding
```

### 3. Generate Discovery Questions

Analyze the HIGH_LEVEL_DESCRIPTION and generate targeted questions. Include ONLY categories relevant to this topic:

**Categories (include if applicable):**

1. **Scope & Objectives** — What's in/out, success criteria
2. **Backend** — API, data model, validation, auth (if BE work)
3. **Frontend** — Components, routing, state, UX (if FE work)
4. **Data & Integration** — Entities, relationships, migrations (if DB work)
5. **UI/UX** — Layout, components, responsive, accessibility (if UI work)
6. **Constraints** — Performance, compatibility, existing patterns

**Question format:**
```markdown
### {Category}

1. [CRITICAL] {Question that must be answered before planning}
2. {Question that helps with planning}
3. {Question about edge cases or preferences}
```

Write questions to DISCOVERY.md.

### 4. Update Root Docs

// turbo
**docs/README.md** — add quick link:
```markdown
- [{FOLDER_SLUG}](./{FOLDER_SLUG}/README.md) — {TOPIC_TITLE}
```

**docs/CHANGELOG.md** — add entry:
```markdown
## {YYYY-MM-DD}
### [{FOLDER_SLUG}]
- Topic scaffolded — discovery phase started
```

**docs/WORK_LOG.md** — add topic under IN PROGRESS milestone:
```markdown
### {TOPIC_TITLE}
{FOLDER_SLUG} ({YYYY-MM-DD} - ongoing)
Linear: {LINEAR_ISSUES or "N/A"}
- [ ] Phase 0 — Discovery & Requirements
```

### 5. Report

```
✅ Topic Created: {TOPIC_TITLE}

📁 docs/{FOLDER_SLUG}/
├── README.md          — hub with quick links
├── PLAN.md            — Phase 0 (discovery)
├── CHANGELOG.md       — initial entry
├── DISCOVERY.md       — {X} questions in {Y} categories
└── SESSION-CHANGES-{DATE}.md

📋 Discovery Questions: {count} total, {critical_count} critical

🎯 Next Steps:
1. Review DISCOVERY.md questions
2. Provide answers (in conversation or edit file)
3. Then: finalize plan OR /kickoff to start research
```

**WAIT for user to answer discovery questions.**

### 6. Finalize Plan (after user answers)

Once the user provides answers:
1. Update DISCOVERY.md with answers
2. Draft complete PLAN.md with phases and baby-step tasks
3. Update WORK_LOG.md with full phase checklist
4. Report the plan and ask for approval

---

## Error Handling

| Error | Action |
|-------|--------|
| Folder already exists | Report, ask: overwrite / merge / cancel |
| Linear MCP unavailable | Skip issue details, use user-provided description |
| No milestone specified | Create topic without milestone association |

## Notes

- This workflow creates the TOPIC only — use `/topic-start` to create branches
- Discovery questions are generated, NOT hardcoded — they adapt to the topic
- Each task in PLAN.md should be 30-60 minutes of work
- For topics not tied to any milestone (pure research), omit MILESTONE_NUMBER
- The `_start_new-topic` helper can be used as an alternative scaffolding engine
