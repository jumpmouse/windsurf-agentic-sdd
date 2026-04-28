---
description: Start a work session — gather context, identify current milestone/topic/phase, find next task
---

# SESSION START

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

Entry point for every work session. Gathers full context before any code work begins.

## Inputs (optional)

- **TOPIC_SLUG**: Jump directly to a specific topic (skip auto-detection)

## Steps

### 1. Gather Root Context

// turbo
Read root documentation:
- `docs/HOW_TO_USE.md` — conventions and structure
- `docs/LINKING-RULES.md` — link format rules
- `docs/README.md` — topic index
- `docs/CHANGELOG.md` — recent changes
- `docs/WORK_LOG.md` — find IN PROGRESS milestone (newest first)

### 2. Identify Current Work

From `WORK_LOG.md`:
- Find the IN PROGRESS milestone (top of file)
- Find the current topic (has date range `YYYY-MM-DD - ongoing`)
- Note which phases are checked/unchecked

### 3. Read Topic Context

// turbo
In the topic folder `docs/{TOPIC_SLUG}/`:
- `README.md` — overview, Linear issues, quick links
- `PLAN.md` — find active phase and first unchecked task
- `CHANGELOG.md` — recent work
- Latest `SESSION-CHANGES-*.md` — last session's state
- `REPORT.md` and any referenced docs (`API-CONTRACT.md`, `DATA-MODEL.md`, etc.)

### 4. Report Status

Report what you found. Be specific — name the milestone, topic, phase, and next task.

```
📍 Session Context:
- Milestone: {milestone number and status}
- Topic: {topic title}
- Branch: {current git branch}
- Phase: {active phase name and status}
- Next Task: {first unchecked task, with file if known}

🎯 Recommended next workflow:
- /kickoff {problem} — if user has a new problem/feature to work on
- /phase-research — if starting fresh investigation on current topic
- /phase-plan — if research is done, ready to create plan
- /phase-plan-verify — if plan exists, needs verification
- /phase-implement — if plan is verified, ready to execute
- /phase-verify — if implementation done, needs verification
- /phase-x-finalize — if verification passed, ready to commit
- /topic-pr — if all phases complete
- /topic-new — if no active topic
```

Pick the ONE most relevant recommendation and explain why.

### 5. Prepare Session File

Create or open today's `SESSION-CHANGES-YYYY-MM-DD.md`.

---

## Notes

- If no IN PROGRESS topic found, suggest `/topic-new` or `/topic-start`
- If all phases complete, suggest `/topic-pr`
- Always read PLAN.md before starting any work
- This is the Cascade-native session start. For Traycer flow, use `/traycer-session-start`
