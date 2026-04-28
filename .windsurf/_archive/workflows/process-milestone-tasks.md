---
description: Process milestone tasks — create topics for each task in task-list-info.md
---

# PROCESS MILESTONE TASKS

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

Reads a milestone's task-list-info.md (already populated) and creates documentation topics for the tasks. This is a sub-workflow called by `/milestone-setup` or run standalone when task-list-info.md already exists.

**Protocol: ALWAYS pause for user approval before creating topics. User controls grouping.**

## Required Input

- **MILESTONE_NUMBER**: The milestone number (e.g., 17)

## Precondition

`docs/UPWORK/milestone {MILESTONE_NUMBER}/task-list-info.md` must exist and be populated. If it doesn't exist, suggest running `/milestone-setup` instead.

## Steps

### 1. Read Task List

// turbo
Read `docs/UPWORK/milestone {MILESTONE_NUMBER}/task-list-info.md`

Parse EACH issue section to extract:

| Field | Source |
|-------|--------|
| Issue ID | `LAN-XXX` from heading or link |
| Title | Issue title text |
| Description | Content between title and Metadata section |
| Status | From `- Status:` line |
| Priority | From `- Priority:` line |
| Labels | From `- Labels:` line |
| Sub-issues | Nested list items with `LAN-YYY` |

### 2. Filter and Categorize

Categorize each task:

| Category | Criteria | Default Action |
|----------|----------|----------------|
| **Needs Work** | Status: "Selected For Development", "In Progress", "Backlog" | Include |
| **Already Done** | Status: "Done", "In Review", "Staging", "Merged" | Group into catch-all or skip |
| **Non-billable** | Label contains "non-billable" | Show but mark — user decides |

Present filtered list:
```
📋 Milestone {MILESTONE_NUMBER} — Task Inventory

NEEDS WORK ({count}):
  1. LAN-XXX: {Title} [{priority}]
     Labels: {labels} | Sub-issues: {count}
  2. LAN-YYY: {Title} [{priority}]
     Labels: {labels} | Sub-issues: {count}

ALREADY DONE ({count}):
  3. LAN-ZZZ: {Title} [{priority}] — {status}

NON-BILLABLE ({count}):
  4. LAN-AAA: {Title} [{priority}]

❓ How to proceed?
   - 'all' — create topics for all "Needs Work" tasks
   - '1,2,5' — process only specific numbers
   - 'group 1+2 as "Topic Name"' — combine tasks into one topic
   - 'include done' — include already-done tasks
   - 'skip' — cancel
```

**WAIT for user decision.**

### 3. Create Topics

For each approved task/group, execute `/topic-new` with:

| Parameter | Value |
|-----------|-------|
| TOPIC_TITLE | Issue title (or user's group name) |
| FOLDER_SLUG | Kebab-case from title |
| HIGH_LEVEL_DESCRIPTION | Issue description + sub-issues + Linear URL |
| LINEAR_ISSUES | LAN-XXX IDs in this group |
| MILESTONE_NUMBER | Current milestone number |

Track progress:
```
Creating topics: {current}/{total}
✅ 1/{total}: "{title}" → docs/{slug}/
✅ 2/{total}: "{title}" → docs/{slug}/
⏳ 3/{total}: creating...
```

If a topic folder already exists for a task, **skip it** and report:
```
⚠️ Skipped: "{title}" — docs/{slug}/ already exists
```

### 4. Update Milestone README

If `docs/UPWORK/milestone {MILESTONE_NUMBER}/README.md` exists, update the Topics table.
If it doesn't exist, create it using the template from `/milestone-setup` Step 7.

### 5. Update Root Docs

// turbo
- `docs/WORK_LOG.md` — add all new topics under milestone section
- `docs/CHANGELOG.md` — add entry for topic creation
- `docs/README.md` — add quick links for new topic folders

### 6. Report

```
✅ Milestone {MILESTONE_NUMBER} — Topics Created

📁 Topics Created: {count}
{numbered list of topics with folder paths}

⚠️ Skipped: {count} (already existed)
{list if any}

📝 Root Docs Updated:
- WORK_LOG.md ✅
- CHANGELOG.md ✅
- README.md ✅

🎯 Next Steps:
1. Review DISCOVERY.md in each topic
2. Provide answers to discovery questions
3. /topic-start {slug} {LAN-XXX} to create branches
4. /kickoff to begin work
```

---

## Error Handling

| Error | Action |
|-------|--------|
| task-list-info.md missing | Suggest `/milestone-setup` first |
| Topic folder already exists | Skip, report, continue |
| No tasks match filter | Report empty result, ask user to adjust |
| /topic-new fails for a task | Report error, continue with remaining tasks |

## Notes

- Sub-issues are included in parent task's topic, NOT as separate topics
- User can combine related tasks into a single topic with custom name
- User can exclude specific tasks by number
- Non-billable tasks are shown but excluded by default
- This workflow is typically called FROM `/milestone-setup` but works standalone
