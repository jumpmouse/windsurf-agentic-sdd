---
description: "[Traycer] Start a work session — gather context, detect Traycer artifacts, identify next task"
---

# SESSION START (Traycer Flow)

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

Entry point for Traycer-based work sessions. Gathers full context and checks for pending Traycer handoff/verification artifacts.

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

### 4. Check for Traycer Artifacts

// turbo
Look in `docs/{TOPIC_SLUG}/plan/` for:
- `handoff/*.md` — pending implementation plans from Traycer
- `verification/*.md` — pending verification reports from Traycer

Note which artifacts are new (not yet addressed in PLAN.md or CHANGELOG.md).

### 5. Report Status

Report what you found. Be specific — name the milestone, topic, phase, and next task.

```
📍 Session Context:
- Milestone: {milestone number and status}
- Topic: {topic title}
- Branch: {current git branch}
- Phase: {active phase name and status}
- Next Task: {first unchecked task, with file if known}

📋 Traycer Artifacts:
- Handoffs: {count or "none"} — {list filenames if any}
- Verifications: {count or "none"} — {list filenames if any}

🎯 Recommended next workflow:
- /traycer-kickoff — if handoff pending, start implementing
- /traycer-phase-implement — if handoff pending, implement specific phase
- /traycer-phase-verify — if verification report pending, fix issues
- /phase-x-finalize — if verification passed, ready to commit
- /topic-pr — if all phases complete
- /topic-new — if no active topic
```

Pick the ONE most relevant recommendation and explain why.

### 6. Prepare Session File

Create or open today's `SESSION-CHANGES-YYYY-MM-DD.md`.

---

## Notes

- If no IN PROGRESS topic found, suggest `/topic-new` or `/topic-start`
- If all phases complete, suggest `/topic-pr`
- Always read PLAN.md before starting any work
- This is the Traycer session start. For Cascade-native flow, use `/session-start`
