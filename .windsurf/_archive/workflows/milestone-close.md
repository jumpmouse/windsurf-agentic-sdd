---
description: Close a milestone — verify all topics done, update docs, generate summary report, log session
---

# MILESTONE CLOSE

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

Complete workflow for closing a milestone. Verifies all topics are done, updates milestone README, generates Upwork summary report, updates root docs, and logs the session.

**Protocol: Every topic must be verified before closing. No milestone closes with open work.**

## Required Inputs

- **MILESTONE_NUMBER**: The milestone number to close (e.g., 23)

## Steps

### 1. Read Milestone Context

// turbo
Read milestone documentation:
- `docs/UPWORK/milestone {MILESTONE_NUMBER}/README.md` — topic list and status
- `docs/UPWORK/milestone {MILESTONE_NUMBER}/task-list-info.md` — original task inventory
- `docs/WORK_LOG.md` — find milestone section, note all topics

Extract:
- **All topic slugs** referenced in the milestone
- **All Linear issue IDs** associated
- **Current status** of each topic in WORK_LOG

### 2. Verify Each Topic

For EACH topic referenced in the milestone:

// turbo
Read `docs/{TOPIC_SLUG}/PLAN.md` and check:
1. **All phases complete?** — Every phase checkbox checked (✅ Done)
2. **All tasks complete?** — Every task checkbox checked
3. **Any ON HOLD items?** — Note them separately

Read `docs/{TOPIC_SLUG}/CHANGELOG.md` and check:
4. **PR created?** — Is there a PR reference in the changelog?
5. **Branch merged?** — Check git status if possible

Build verification table:

```markdown
## Topic Verification

| # | Topic | Phases | Tasks | PR | Branch | Status |
|---|-------|--------|-------|----|--------|--------|
| 1 | {slug} | 3/3 ✅ | 12/12 ✅ | #45 ✅ | merged ✅ | ✅ DONE |
| 2 | {slug} | 2/3 ⚠️ | 8/10 ⚠️ | — ❌ | open ❌ | ⚠️ INCOMPLETE |
```

### 3. Report Verification Results

Present results to user:

**If ALL topics complete:**
```
✅ Milestone {MILESTONE_NUMBER} — All topics verified

📊 Summary:
- Topics: {count} / {count} complete
- Tasks: {total_done} / {total} complete
- PRs: {count} merged

Ready to close. Proceed? (yes/no)
```

**If ANY topic incomplete:**
```
⚠️ Milestone {MILESTONE_NUMBER} — Incomplete items found

📊 Summary:
- Topics: {done}/{total} complete
- Incomplete:
  - {topic-slug}: Phase {N} has {X} unchecked tasks
  - {topic-slug}: No PR created

❓ Options:
  1. Close anyway (mark incomplete items as deferred)
  2. Fix incomplete items first
  3. Cancel
```

**WAIT for user decision before proceeding.**

### 4. Update Milestone README

Update `docs/UPWORK/milestone {MILESTONE_NUMBER}/README.md`:

- Change status: `**Status:** ✅ Complete`
- Add completion date to timeline
- Update topic status table (all ✅ or note deferred items)
- Add summary of deferred items if any

### 5. Generate Summary Report

Execute `/milestone-report` workflow with:
- **MILESTONE_NUMBER**: {MILESTONE_NUMBER}
- Context from verified topics and task-list-info.md

This creates `docs/UPWORK/milestone {MILESTONE_NUMBER}/REPORTS/MILESTONE-{N}-SUMMARY.md`

### 6. Update Root Docs

Update `docs/WORK_LOG.md`:
- Change milestone status from IN PROGRESS to ✅ DONE
- Add completion date
- Ensure all topic phases are checked

Update `docs/CHANGELOG.md`:
- Add date entry: "Milestone {N} closed — {summary}"

### 7. Archive Topics (optional)

Ask user if they want to:
- Move completed topic folders to an archive
- Or leave them in place (default)

### 8. Log Session

Execute `/session-log` to reconcile all documentation changes.

### 9. Final Report

```
✅ Milestone {MILESTONE_NUMBER} Closed

📊 Final Summary:
- Topics completed: {count}
- Total tasks completed: {count}
- PRs merged: {count}
- Period: {start_date} — {end_date}

📄 Report: docs/UPWORK/milestone {MILESTONE_NUMBER}/REPORTS/MILESTONE-{N}-SUMMARY.md

📝 Docs Updated:
- docs/WORK_LOG.md — milestone marked ✅ DONE
- docs/CHANGELOG.md — closure entry added
- Milestone README — status updated

🎯 Next:
- Review summary report for Upwork submission
- Start next milestone: /milestone-setup {NEXT_NUMBER}
```

---

## Error Handling

| Error | Action |
|-------|--------|
| Milestone folder not found | Report error, suggest /milestone-setup first |
| No topics referenced | Check WORK_LOG for topics under this milestone |
| Topic folder missing | Report missing folder, continue with available topics |
| milestone-report fails | Generate basic summary inline, note the error |

## Notes

- ALWAYS verify topics before closing — never close blindly
- Incomplete items can be deferred to next milestone if user approves
- Summary report follows the template in `.windsurf/_templates/milestone-summary-template.md`
- This workflow updates Linear issue status if user approves (via Linear MCP)
