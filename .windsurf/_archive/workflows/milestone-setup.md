---
description: Set up a new milestone — fetch Linear tasks, populate task list, analyze, group into topics, scaffold everything
---

# MILESTONE SETUP

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

Complete workflow for initiating a new milestone. Fetches tasks from Linear, populates task-list-info.md, analyzes and groups tasks into topics, scaffolds topic folders, creates milestone README, and updates root docs.

**Protocol: Every step produces a verifiable artifact. No step is skipped.**

## Required Inputs

- **MILESTONE_NUMBER**: The milestone number (e.g., 23)
- **MILESTONE_TITLE** (optional): Human-readable title (auto-detect from Linear label if not provided)

## Steps

### 1. Create Milestone Folder

// turbo
Create `docs/UPWORK/milestone {MILESTONE_NUMBER}/` if it doesn't exist.

Create these files:
- `README.md` — milestone hub (populated in Step 7)
- `task-list-info.md` — task inventory (populated in Step 3)
- `REPORTS/` — folder for summary reports (empty for now)

If folder already exists, read existing files and report what's already there. Do NOT overwrite existing content.

### 2. Fetch Tasks from Linear

Use Linear MCP to find all issues for this milestone:

```
mcp2_list_issues with label: "MILESTONE {MILESTONE_NUMBER}"
```

Also check for supplementary labels:
```
mcp2_list_issues with label: "MILESTONE {MILESTONE_NUMBER}-add"
```

For EACH issue found:
1. Read full issue details via `mcp2_get_issue`
2. Collect: ID, title, description, status, priority, labels, assignee
3. Check for sub-issues (child issues)
4. Note any linked issues or dependencies

**If Linear MCP fails**, report the error and ask user to provide task list manually.

### 3. Populate task-list-info.md

Write all fetched tasks to `docs/UPWORK/milestone {MILESTONE_NUMBER}/task-list-info.md` using this exact format:

```markdown
# Milestone {MILESTONE_NUMBER} — Task List

**Fetched from Linear:** {DATE}
**Label filter:** MILESTONE {MILESTONE_NUMBER}
**Total issues:** {COUNT}

---

## Tasks

- [LAN-{XXX}: {Issue Title}](https://linear.app/land-of-tales/issue/LAN-{XXX})

  {Issue description — first 2-3 paragraphs}

  **Metadata:**
  - Status: {status}
  - Priority: {priority}
  - Labels: {labels comma-separated}
  - Assignee: {name or "Unassigned"}

  **Sub-issues:**
  - LAN-{YYY}: {sub-issue title} [{status}]
  - LAN-{ZZZ}: {sub-issue title} [{status}]

---

{Repeat for each issue}
```

### 4. Analyze Tasks

For each task, perform **lightweight** analysis (NOT deep research — just enough to understand scope):

1. **Area**: Backend / Frontend / Full-stack / Docs / DevOps
2. **Component**: Which part of the app (e.g., "carousels", "admin panel", "auth", "reader")
3. **Complexity**: Simple / Medium / Complex
4. **Dependencies**: Does this task depend on another task?
5. **Status**: Already done (staging/review) vs. needs work

### 5. Group into Topics

Based on analysis, propose topic groupings. Follow these rules:

| Condition | Action |
|-----------|--------|
| Task is complex (M/L), standalone area | **Separate topic** |
| 2-3 tasks touch the same component | **Group into one topic** |
| Task is simple (XS/S) fix | **Group with related tasks** |
| Task is already done (staging/merged) | **Group into "{milestone}-fixes" catch-all topic** or skip |
| Research/investigation task | **Separate topic** |

Present grouping to user:

```
📋 Milestone {MILESTONE_NUMBER} — Proposed Topic Grouping

Topic 1: "{Topic Title}" (from LAN-XXX, LAN-YYY)
  - Area: {area}
  - Complexity: {complexity}
  - Reason: {why grouped}

Topic 2: "{Topic Title}" (from LAN-ZZZ)
  - Area: {area}
  - Complexity: {complexity}
  - Reason: {standalone — complex feature}

Topic 3: "M{N} Fixes" (from LAN-AAA, LAN-BBB) [CATCH-ALL]
  - Small fixes grouped together

❓ Approve this grouping?
   - 'yes' — proceed with all
   - Adjust: "merge topic 1 and 2" / "separate LAN-XXX" / etc.
   - 'skip' — skip topic creation, just keep task-list-info
```

**WAIT for user approval before proceeding.**

### 6. Create Topics

For each approved topic group, execute `/topic-new` with:

- **TOPIC_TITLE**: The proposed title
- **FOLDER_SLUG**: Generated kebab-case from title
- **HIGH_LEVEL_DESCRIPTION**: Combined from:
  - Issue descriptions from task-list-info.md
  - Sub-issues list
  - Linear URLs for reference
  - Area and complexity context
- **LINEAR_ISSUES**: List of LAN-XXX IDs in this group

Track progress:
```
Creating topics: {N}/{TOTAL}
✅ Topic 1: "{title}" → docs/{slug}/
✅ Topic 2: "{title}" → docs/{slug}/
⏳ Topic 3: creating...
```

### 7. Create Milestone README

Write `docs/UPWORK/milestone {MILESTONE_NUMBER}/README.md`:

```markdown
# Milestone {MILESTONE_NUMBER} — {MILESTONE_TITLE}

**Status:** 🔄 In Progress
**Created:** {DATE}
**Linear Label:** MILESTONE {MILESTONE_NUMBER}

---

## Overview

{Brief description of milestone scope — what this milestone delivers}

## Topics

| # | Topic | Linear Issues | Status |
|---|-------|--------------|--------|
| 1 | [{Topic Title}](../../{topic-slug}/README.md) | LAN-XXX, LAN-YYY | ⏳ Not Started |
| 2 | [{Topic Title}](../../{topic-slug}/README.md) | LAN-ZZZ | ⏳ Not Started |

## Task List

See [task-list-info.md](./task-list-info.md) for full task inventory.

## Documentation References

| Topic | GitHub URL |
|-------|-----------|
| {topic-slug} | [GitHub](https://github.com/landoftales/landoftales.github.io/blob/master/{topic-slug}/README.md) |

---

## Timeline

| Date | Event |
|------|-------|
| {DATE} | Milestone created, topics scaffolded |
```

### 8. Update Root Docs

// turbo
Update `docs/WORK_LOG.md`:
- Add milestone section at top (IN PROGRESS)
- Add all topics with phase checklists
- Format: follow existing WORK_LOG conventions

Update `docs/CHANGELOG.md`:
- Add date entry with milestone setup note

Update `docs/README.md`:
- Add quick links for all new topic folders

### 9. Report

```
✅ Milestone {MILESTONE_NUMBER} Setup Complete

📋 Linear Tasks: {total_count}
📁 Topics Created: {topic_count}
📝 Files Created:
  - docs/UPWORK/milestone {MILESTONE_NUMBER}/README.md
  - docs/UPWORK/milestone {MILESTONE_NUMBER}/task-list-info.md
  - {list each topic folder}

📊 Topic Summary:
{table of topics with linear IDs and status}

🎯 Suggested First Task:
  - {topic with "In Progress" issue, or highest priority}
  - Run: /kickoff {problem description}
  - Or: /topic-start {topic-slug} {LINEAR_ID} to prepare branch

❓ What would you like to work on first?
```

---

## Error Handling

| Error | Action |
|-------|--------|
| Linear MCP unavailable | Ask user to provide tasks manually or retry |
| Milestone folder already exists | Read existing, report, ask before overwriting |
| Topic folder already exists | Skip that topic, report it |
| No tasks found for label | Check alternative labels, ask user |

## Notes

- This workflow ALWAYS pauses for user approval at Step 5 (grouping)
- Tasks already in staging/review are grouped into catch-all topic or skipped per user choice
- Sub-issues are included in parent task's topic, NOT as separate topics
- For re-running on existing milestone, existing topics are preserved
