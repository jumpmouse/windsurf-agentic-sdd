---
description: Generate milestone summary report for Upwork payment request and detailed technical wrap-up
---

# MILESTONE REPORT

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

Generate a comprehensive milestone summary report for Upwork payment requests. Uses the milestone summary template and populates it from milestone data, topic changelogs, and Linear.

**Protocol: Report uses EXACTLY the template structure from `.windsurf/_templates/milestone-summary-template.md`. No improvisation on format.**

## Required Inputs

- **MILESTONE_NUMBER**: The milestone number (e.g., 26)

## Optional Inputs

- **PERIOD_START**: Start date "Month DD, YYYY" (default: extract from earliest topic CHANGELOG date)
- **PERIOD_END**: End date "Month DD, YYYY" (default: today)
- **ESTIMATED_TIME**: From task-list-info.md or user (e.g., "2 days")
- **ACTUAL_TIME**: Actual time spent (e.g., "3 days")

## Steps

### 1. Load Template

// turbo
Read `.windsurf/_templates/milestone-summary-template.md` — this is the EXACT structure to follow.

### 2. Gather Milestone Data

// turbo
Read milestone folder:
- `docs/UPWORK/milestone {MILESTONE_NUMBER}/README.md` — topic list, overview
- `docs/UPWORK/milestone {MILESTONE_NUMBER}/task-list-info.md` — task inventory with Linear metadata

Create `docs/UPWORK/milestone {MILESTONE_NUMBER}/REPORTS/` folder if it doesn't exist.

If milestone README doesn't exist, check `docs/WORK_LOG.md` for topics under this milestone.

### 3. Gather Topic Data

// turbo
For EACH topic referenced in the milestone:
1. Read `docs/{TOPIC_SLUG}/CHANGELOG.md` — all changes made (this is the primary data source)
2. Read `docs/{TOPIC_SLUG}/PLAN.md` — phases and completion status
3. Read `docs/{TOPIC_SLUG}/README.md` — Linear issues, overview

Build a data structure:
```
Topic: {slug}
  Linear IDs: [LAN-XXX, LAN-YYY]
  Phases: {count done}/{count total}
  Changes: [list from CHANGELOG]
  Period: {earliest date} — {latest date}
```

### 4. Gather Linear Data

If Linear MCP available:
- Fetch issue details for each LAN-XXX referenced: `mcp2_get_issue`
- Collect: title, status, description

If Linear MCP unavailable, use data from task-list-info.md.

### 5. Group into Deliverables

Group related tasks into logical deliverables for the client:

| Grouping Rule | Deliverable Name |
|---------------|-----------------|
| Multiple bugs in same component | "{Component} Improvements" |
| Feature implementation | "{Feature} Implementation" |
| Single standalone task | Use task title directly |
| Research/investigation | "{Area} Investigation" |

Each deliverable needs:
- **Title** — client-friendly name
- **Linear IDs** — all LAN-XXX in this group
- **Problem** — what was broken/missing (business terms)
- **Solution** — what was delivered (brief, non-technical)
- **Changes** — bullet list of specific changes (from CHANGELOGs)

### 6. Calculate Metrics

Count from gathered data:
- Files modified (Backend): count unique BE file paths from SESSION-CHANGES
- Files modified (Frontend): count unique FE file paths from SESSION-CHANGES
- Linear issues resolved: count of LAN-XXX IDs
- Build status: ✅ Passing (verify if possible)

### 7. Write Report

Create `docs/UPWORK/milestone {MILESTONE_NUMBER}/REPORTS/MILESTONE-{MILESTONE_NUMBER}-SUMMARY.md`

Fill the template EXACTLY:

```markdown
# Milestone {NUMBER} — Work Summary
**Period:** {PERIOD_START} – {PERIOD_END} ({DAYS} days)
**Status:** ✅ Complete
**Estimated time:** {ESTIMATED_TIME}
**Actual time:** {ACTUAL_TIME}

**Note:**
{Any relevant notes about the milestone}

---

## What Was Delivered

| # | Deliverable | Linear Issue | Status |
|---|-------------|--------------|--------|
| 1 | **{Deliverable Title}** | {LAN-XXX, LAN-YYY} | ✅ Done |
{repeat for each deliverable}

---

## 1. {Deliverable Title}

{Brief description}

### Problem Solved
{Business-terms description of what was broken/missing}

### Solution Delivered
- **{Aspect 1}** — {Description}
- **{Aspect 2}** — {Description}

### Backend Changes
- {Change from CHANGELOG}

### Frontend Changes
- {Change from CHANGELOG}

---

{repeat for each deliverable}

---

## Technical Summary

| Metric | Value |
|--------|-------|
| Files Modified (Backend) | {COUNT} |
| Files Modified (Frontend) | {COUNT} |
| Linear Issues Resolved | {COUNT} |
| Build Status | ✅ Passing |

---

## Future Work (Out of Scope for This Milestone)

1. **{Deferred item}** — {Brief reason}
{from "Suggestions" sections in phase reports}

---

## Linear Tasks

| ID | Task | URL |
|----|------|-----|
| LAN-{XX} | {Task title} | [Link](https://linear.app/land-of-tales/issue/LAN-{XX}) |

---

## Documentation References

| Topic | URL |
|-------|-----|
| {topic-slug} | [GitHub](https://github.com/landoftales/landoftales.github.io/blob/master/{topic-slug}/README.md) |

---

*Report generated: {YYYY-MM-DD}*
```

**URL Rules:**
- Linear URLs: `https://linear.app/land-of-tales/issue/LAN-{XX}`
- GitHub doc URLs: `https://github.com/landoftales/landoftales.github.io/blob/master/{topic-slug}/README.md`
- NEVER use `landoftales.github.io` URLs (that's the docs site, not the repo)

### 8. Verification

Before reporting, verify:
- [ ] Template structure matches `.windsurf/_templates/milestone-summary-template.md`
- [ ] Every Linear issue from task-list-info.md appears in the report
- [ ] Every topic referenced in milestone appears in Documentation References
- [ ] All URLs are correctly formatted
- [ ] Report language is client-facing (non-technical where possible)
- [ ] Generation timestamp at bottom

### 9. Report

```
✅ Milestone {MILESTONE_NUMBER} report generated

📄 Report: docs/UPWORK/milestone {MILESTONE_NUMBER}/REPORTS/MILESTONE-{MILESTONE_NUMBER}-SUMMARY.md

📊 Stats:
- Linear tasks: {count}
- Deliverables: {count}
- Topics covered: {count}
- Period: {start} — {end}

📋 Next: Review report, then submit for Upwork payment
```

---

## Error Handling

| Error | Action |
|-------|--------|
| Template file missing | Use inline template structure from this workflow |
| No task-list-info.md | Gather data from WORK_LOG and topic docs only |
| Linear MCP unavailable | Use data from task-list-info.md |
| Topic CHANGELOG missing | Note gap, use PLAN.md data instead |
| Missing time estimates | Leave placeholders, ask user |

## Notes

- Report language is CLIENT-FACING — minimize jargon, explain in business terms
- Include ALL Linear task IDs for traceability
- Backend/Frontend change sections are OPTIONAL per deliverable — omit if not applicable
- Future Work items come from "Suggestions" sections in phase implementation reports
- This workflow is typically called from `/milestone-close` but can be run standalone
