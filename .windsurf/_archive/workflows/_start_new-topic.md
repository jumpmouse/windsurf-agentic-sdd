---
description: Scaffold a new docs milestone task folder from templates with intelligent discovery
---

# NEW TOPIC INITIALIZATION — SCAFFOLDING HELPER

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

Low-level helper workflow that scaffolds a topic folder structure and populates templates. Called by `/topic-new` or can be used standalone.

**Protocol: This is a scaffolding engine — it creates files and populates templates. Discovery logic lives in `/topic-new`.**

## Required Inputs

- **TOPIC_TITLE**: Human-readable title (e.g., "Admin Assets Grid View")
- **FOLDER_SLUG**: Kebab-case folder name (e.g., "admin-assets-grid")
- **HIGH_LEVEL_DESCRIPTION**: 2-4 paragraphs covering problem, feature, scope, constraints

## Optional Inputs

- **LINEAR_ISSUES**: List of LAN-XXX IDs
- **MILESTONE_NUMBER**: Associated milestone number
- **BRANCH_NAME**: If branch already exists

## PHASE 1: Scaffolding

### Step 1.1: Scaffold Folder Structure

// turbo
Create `docs/{FOLDER_SLUG}/` with these files:
- `README.md` — topic hub
- `PLAN.md` — skeleton with Phase 0
- `CHANGELOG.md` — initial entry
- `DISCOVERY.md` — requirements gathering
- `SESSION-CHANGES-{DATE}.md` — today's session file

### Step 1.2: Populate Templates

Use templates at end of this workflow. Replace ALL placeholders:
- `{TOPIC_TITLE}` → actual title
- `{FOLDER_SLUG}` → actual slug
- `{DATE}` → YYYY-MM-DD
- `{HIGH_LEVEL_DESCRIPTION}` → actual description
- `{LINEAR_ISSUES}` → LAN-XXX links or "N/A"
- `{MILESTONE_NUMBER}` → number or "N/A"
- `{BRANCH_NAME}` → branch name or "TBD"

### Step 1.3: Update Root Documentation

// turbo
- `docs/README.md` — add quick link to `./{FOLDER_SLUG}/README.md`
- `docs/CHANGELOG.md` — add `[{FOLDER_SLUG}]` scaffolding entry under today's date
- `docs/WORK_LOG.md` — add topic under IN PROGRESS milestone with `({DATE} - ongoing)`

## PHASE 2: Intelligent Discovery & Question Generation

### Step 2.1: Analyze High-Level Description

As an expert CTO/architect, analyze the HIGH_LEVEL_DESCRIPTION and identify:

1. **Component Types Involved**:
   - Backend API work? (controllers, services, repositories, DTOs)
   - Frontend work? (components, services, routing, state management)
   - Database changes? (migrations, new tables, schema updates)
   - Integration points? (existing features to connect/modify)
   - Infrastructure? (storage, caching, authentication)

2. **Technical Complexity Indicators**:
   - New vs. enhancement of existing feature
   - Data model complexity
   - UI/UX complexity
   - Performance considerations
   - Security/authorization requirements

3. **Knowledge Gaps** (what's unclear from the description):
   - Missing technical requirements
   - Unclear scope boundaries
   - Unknown dependencies
   - Ambiguous success criteria

### Step 2.2: Generate Targeted Discovery Questions

Based on your analysis, generate a comprehensive but focused list of questions organized by category. Include ONLY questions that are relevant to this specific topic.

**Question Categories & Framework:**

1. **Scope & Objectives**
   - What is the primary user/admin problem being solved?
   - What are the must-have vs. nice-to-have features?
   - What is explicitly OUT of scope?
   - What defines success for this feature?

2. **Backend Requirements** (if applicable)
   - Are new database tables/columns needed? What entities?
   - What API endpoints are needed? (list, create, update, delete, etc.)
   - What authorization/roles are required?
   - Are there performance requirements? (pagination, caching, indexing)
   - Integration with existing services/repositories?
   - Data validation rules?

3. **Frontend Requirements** (if applicable)
   - Where does this live in the admin/public UI? New route or enhancement?
   - Which existing components can be reused?
   - What are the key user interactions? (filters, sort, search, bulk actions)
   - Form fields and validation requirements?
   - Modal vs. inline editing?
   - Responsive/mobile considerations?
   - Loading/error states?

4. **Data & Integration**
   - What existing data/entities does this consume?
   - What are the relationships between entities?
   - Migration strategy if altering existing data?
   - Backward compatibility requirements?

5. **UI/UX Specifics**
   - Layout and visual design requirements?
   - Which PrimeNG/UI components to use?
   - Sorting/filtering/pagination behavior?
   - Empty states and error handling?
   - Success feedback (toasts, notifications)?

6. **Technical Constraints**
   - Browser/device compatibility requirements?
   - Performance targets? (load time, response time)
   - Accessibility requirements?
   - Existing code patterns to follow?

7. **Testing & Validation**
   - What should be unit tested?
   - What requires integration testing?
   - Manual testing checklist?
   - Edge cases to handle?

### Step 2.3: Write Discovery Questions to DISCOVERY.md

Populate `docs/{FOLDER_SLUG}/DISCOVERY.md` with:
- Analysis summary (component types, complexity, gaps)
- Generated questions organized by category
- Note which questions are critical vs. optional

### Step 2.4: Report to User

Provide summary:
```
✅ Topic scaffolded: docs/{FOLDER_SLUG}/
✅ Files created: README, PLAN (skeleton), CHANGELOG, REPORT, DISCOVERY, SESSION
✅ Root docs updated: README, CHANGELOG, WORK_LOG

📋 Discovery Analysis:
- Components involved: [list]
- Estimated complexity: [Low/Medium/High]
- Key unknowns: [list 3-5 critical gaps]

🎯 Next Steps:
Please review DISCOVERY.md and provide answers to the questions.
Focus on the [CRITICAL] questions first.

Once you provide answers, I will:
1. Draft detailed PLAN.md with phases and baby-step tasks
2. Complete REPORT.md investigation framework
3. Identify integration points and dependencies
4. Create initial API/data model design (if needed)
5. Report back and ask if you want to discuss before starting Phase 1
```

## PHASE 3: Finalization (after user provides answers)

### Step 3.1: Update REPORT.md

- Fill in Investigation section with baseline analysis
- Document key decisions from discovery answers
- List integration points and dependencies
- Outline user impacts and technical approach

### Step 3.2: Build Comprehensive PLAN.md

Based on answers, create phases with baby-step tasks:

**Standard Phase Structure:**
- Phase 0: Alignment/Baseline/Investigation (if needed)
- Phase 1: Backend data model & migrations (if needed)
- Phase 2: Backend API endpoints (if needed)
- Phase 3: Frontend components & state (if needed)
- Phase 4: Integration & wiring
- Phase 5: Polish, testing, documentation

**Task Granularity:**
- Each task = 30-60 minutes of work
- If >10 tasks in a phase, group into sub-phases
- Include verification/testing tasks
- Mark dependencies between tasks

### Step 3.3: Create Supporting Documents (if needed)

Based on complexity:
- `API-CONTRACT.md` — endpoint specs, request/response DTOs
- `DATA-MODEL.md` — entities, relationships, migrations
- `UI-SPEC.md` — component hierarchy, state management, user flows
- `INTEGRATION-MAP.md` — existing code touchpoints

### Step 3.4: Update WORK_LOG.md

Add phase checklist to topic section (all unchecked initially)

### Step 3.5: Final Report to User

Provide summary:
```
✅ Topic fully initialized: {TOPIC_TITLE}

📁 Documentation Structure:
- PLAN.md: {X} phases, {Y} tasks
- REPORT.md: Investigation framework complete
- [Additional docs created]

🎯 First Phase: {Phase 0 or Phase 1 name}
Tasks:
1. [First 3-5 tasks listed]

❓ Questions for you:
1. Does the phasing make sense?
2. Any concerns about scope or approach?
3. Ready to start Phase {0 or 1}, or want to discuss anything?

Awaiting your go-ahead to begin execution.
```

## PHASE 4: Verify All Links

- Ensure all back links exist
- Verify root README quick link works
- Check WORK_LOG formatting
- Validate all internal topic links

---

# TEMPLATES

Replace ALL placeholders: `{TOPIC_TITLE}`, `{FOLDER_SLUG}`, `{DATE}`, `{HIGH_LEVEL_DESCRIPTION}`, `{LINEAR_ISSUES}`, `{MILESTONE_NUMBER}`, `{BRANCH_NAME}`

## README.md
```markdown
# {TOPIC_TITLE}

## Overview

{HIGH_LEVEL_DESCRIPTION}

**Linear Issues:** {LINEAR_ISSUES or "N/A"}
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
```

## PLAN.md
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

## CHANGELOG.md
```markdown
# Changelog — {TOPIC_TITLE}
[← Back to README](./README.md)

## {DATE}
- Topic scaffolded — discovery questions generated
- Awaiting requirements input to finalize plan
```

## DISCOVERY.md
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

{AI-generated targeted questions — organized by relevant categories only}

---

## Answers

_User provides answers here or in conversation._
```

## SESSION-CHANGES-{DATE}.md
```markdown
# Session Changes — {DATE}
[← Back to README](./README.md)

## Files Changed

### docs
- `docs/{FOLDER_SLUG}/README.md` — created
- `docs/{FOLDER_SLUG}/PLAN.md` — created
- `docs/{FOLDER_SLUG}/CHANGELOG.md` — created
- `docs/{FOLDER_SLUG}/DISCOVERY.md` — created
- `docs/{FOLDER_SLUG}/SESSION-CHANGES-{DATE}.md` — created

## Context
- Phase: Phase 0 — Discovery
- Tasks completed: Topic scaffolding

## Notes
- Root docs updated: README, CHANGELOG, WORK_LOG
```

---

## Error Handling

| Error | Action |
|-------|--------|
| Folder already exists | Report, ask: overwrite / merge / cancel |
| Root docs missing | Create minimal versions |
| WORK_LOG has no IN PROGRESS milestone | Add topic without milestone association |

## Notes

- This is a LOW-LEVEL helper — `/topic-new` is the preferred entry point
- Templates are kept in sync with `/topic-new` inline templates
- The REPORT.md template was removed — reports are generated during phase work, not at scaffolding time
