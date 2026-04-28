---
description: Reconcile docs after work — update plan, changelogs, session files, and root docs
---

# SESSION LOG

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

Reconcile all documentation after a work session. Updates topic docs, root docs, and ensures consistency across all documentation layers.

**Protocol: Every doc update uses the exact templates below. Consistency is mandatory.**

## Inputs (optional)

- **FOLDER_SLUG**: Topic folder to reconcile (default: auto-detect most recently changed folder under `docs/` that contains a `PLAN.md`)
- **DATE**: Override date in `YYYY-MM-DD` (default: today)

## Steps

### 1. Identify Target Folder

// turbo
- If FOLDER_SLUG provided, use `docs/{FOLDER_SLUG}/`
- Else, find the most recently modified subfolder in `docs/` containing both `PLAN.md` and `CHANGELOG.md`
- Read `docs/WORK_LOG.md` to confirm the folder belongs to the IN PROGRESS milestone
- If no folder found, report error and ask user to specify

### 2. Review Current State

// turbo
Read these files to understand what happened:
- `docs/{FOLDER_SLUG}/PLAN.md` — active phase, completed vs unchecked tasks
- `docs/{FOLDER_SLUG}/CHANGELOG.md` — last date section (to avoid duplicates)
- Latest `docs/{FOLDER_SLUG}/SESSION-CHANGES-*.md` — files from last session
- `docs/WORK_LOG.md` — current phase status for this topic

Build a mental diff: what changed since the last CHANGELOG entry?

### 3. Update PLAN.md

Mark task status using ONLY these conventions:
- `- [x]` — completed
- `- [ ]` — not started / in progress
- `- [ ] ~~Task~~ — ON HOLD: {reason}` — deferred
- `- [ ] ~~Task~~ — SKIPPED: {reason}` — intentionally skipped

Update phase status headers:
- `**Status:** ✅ Done` — all tasks checked
- `**Status:** 🔄 In Progress` — some tasks checked
- `**Status:** ⏳ Not Started` — no tasks checked

**Do NOT add new tasks or change existing task descriptions** unless the user explicitly made scope changes during the session.

### 4. Update CHANGELOG.md

Add entries under today's date. Use this exact format:

```markdown
## {YYYY-MM-DD}

### Phase {N} — {Phase Name}
- {What was done} — `{file path}` {brief detail}
- {What was done} — `{file path}` {brief detail}

### Decisions
- {Decision made and rationale} (if any decisions were made)

### Issues Found
- {Issue description} — {resolution or "deferred to Phase X"} (if any)
```

**Rules:**
- If date section already exists, APPEND to it (don't create duplicate)
- Every bullet references at least one file path
- Group by phase if multiple phases were worked on
- Keep descriptions concise — one line per change

### 5. Update SESSION-CHANGES-{DATE}.md

Create or update `docs/{FOLDER_SLUG}/SESSION-CHANGES-{YYYY-MM-DD}.md`:

```markdown
# Session Changes — {YYYY-MM-DD}
[← Back to README](./README.md)

## Files Changed

### {Repo} ({fe|be|docs})
- `{relative/path/to/file}` — {what changed}
- `{relative/path/to/file}` — {what changed}

## Context
- Phase: {phase number and name}
- Tasks completed: {list task IDs}
- Branch: {current branch name}

## Notes
- {Any relevant context, blockers, or decisions}
```

If file already exists for today, append new files to the existing list.

### 6. Update Root WORK_LOG.md

// turbo
In `docs/WORK_LOG.md`, find the current topic under the IN PROGRESS milestone:

- **Check phase checkbox** if ALL tasks in that phase are Done/Skipped/On Hold
- **Do NOT check** if any task is still unchecked and active
- **Add note** if a phase was reopened: `(reopened: {reason})`
- Keep the summary line current: `{topic-slug} ({date} - ongoing)` stays as-is until topic closes

### 7. Mirror to Root CHANGELOG.md

// turbo
In `docs/CHANGELOG.md`:

- Find or create date section `## {YYYY-MM-DD}`
- Add subsection `### [{FOLDER_SLUG}]`
- Add concise bullets mirroring the topic CHANGELOG entries (shortened — 1 line per major change)

**Format:**
```markdown
## {YYYY-MM-DD}

### [{folder-slug}]
- {Concise summary of change 1}
- {Concise summary of change 2}
```

### 8. Link Hygiene Check

Verify these links exist and resolve:
- [ ] Every `.md` in topic folder (except SESSION-CHANGES) starts with `[← Back to README](./README.md)`
- [ ] Topic README links to: PLAN.md, CHANGELOG.md, latest SESSION-CHANGES file
- [ ] WORK_LOG topic entry links match actual folder structure

Fix any broken links found.

### 9. Verification

Before reporting, confirm:
- [ ] PLAN.md task checkboxes match actual work done
- [ ] CHANGELOG.md has entry for today with file references
- [ ] SESSION-CHANGES file exists for today
- [ ] WORK_LOG.md phase checkboxes are accurate
- [ ] Root CHANGELOG.md mirrored
- [ ] No broken links

### 10. Report

```
✅ Session Logged

📁 Topic: docs/{FOLDER_SLUG}/
📅 Date: {DATE}

📝 Updated:
- PLAN.md — {X} tasks marked complete
- CHANGELOG.md — {Y} entries added
- SESSION-CHANGES-{DATE}.md — {Z} files listed
- WORK_LOG.md — Phase {N} {checked/unchanged}
- Root CHANGELOG.md — mirrored

🎯 Current State:
- Phase {N}: {status} ({done}/{total} tasks)
- Next task: {first unchecked task or "all done"}
```

---

## Error Handling

| Error | Action |
|-------|--------|
| No target folder found | Ask user to specify FOLDER_SLUG |
| PLAN.md missing | Create minimal one from WORK_LOG context, warn user |
| CHANGELOG.md missing | Create with today's date section |
| WORK_LOG.md topic not found | Add topic entry under current milestone |

## Notes

- Run this at the end of every work session
- Can be combined with `/phase-x-finalize` (which handles commits)
- Does NOT create commits — only updates documentation files
- If multiple topics were worked on, run once per topic or specify each FOLDER_SLUG
