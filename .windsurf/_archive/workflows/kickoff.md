---
description: Start working on a problem or feature — full depth-first cycle with investigation, planning, verification, and implementation
---

# KICKOFF — Cascade-Native Development Flow

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

Universal entry point for the Cascade-native workflow. Handles two modes:
1. **New work** — user provides a PROBLEM → gathers context → launches `/phase-research`
2. **Resume** — no PROBLEM provided → gathers context → automatically continues from where we left off

**Philosophy: Depth over speed.** Check 3 times rather than skip once. Be methodical, not fast.

## Inputs

- **PROBLEM** (optional): The problem description, feature request, or bug report. If not provided, kickoff resumes from the last known state.
- **TOPIC_SLUG** (optional): Topic folder name (auto-detect from WORK_LOG.md if not provided)

## Full Lifecycle

```
/kickoff (this workflow — context + route to next phase)
  → /phase-research (@deep-investigator)     ← user reviews findings
  → /phase-plan (@project-planner)           ← user reviews plan
  → /phase-plan-verify (@verification-fixer) ← verify plan before implementation
  → /phase-implement (@spec-implementer)     ← execute verified plan
  → /phase-verify (@verification-fixer)      ← verify implementation
  → /phase-x-finalize (@code-reviewer)       ← commit, close
```

## Steps

### 0. Confirm Execution Mode

// turbo
**MANDATORY — Do this FIRST before any other work.**

Print the following message to the user, exactly:
```
🔒 KICKOFF WORKFLOW — STRICT EXECUTION MODE
Following mandatory step-by-step protocol. No steps will be skipped.
```

Then proceed to Step 1. Do NOT start any investigation, code reading, or other work until Step 1.

### 1. Gather Session Context

// turbo
Execute `/session-start` — this reads all root docs, identifies the current milestone/topic/phase, and reports status.

**Do NOT duplicate what /session-start already does.** It reads WORK_LOG.md, topic README, PLAN.md, CHANGELOG, and SESSION-CHANGES files. Use its output as your context.

### 2. Determine Mode

Based on the user's message and session-start findings:

**Mode A — New Work** (user provided a PROBLEM):
- The user has a specific problem, feature, or bug to work on
- Proceed to Step 3A

**Mode B — Resume** (no PROBLEM provided):
- The user wants to continue from where we left off
- Use session-start's findings to determine the next workflow
- Proceed to Step 3B

### 3A. New Work → Launch Research

Execute `/phase-research` with the gathered context and the user's PROBLEM statement.

After research completes, this workflow **STOPS** — user reviews findings and answers questions before proceeding.

### 3B. Resume → Continue from Last State

Based on session-start's phase analysis, determine and launch the next workflow:

| Current State | Next Action |
|---------------|-------------|
| No topic exists | Suggest `/topic-new` and ask user what to work on |
| Topic exists, no research done | Launch `/phase-research` |
| Research done, no plan | Launch `/phase-plan` |
| Plan exists, not verified | Launch `/phase-plan-verify` |
| Plan verified, tasks remain | Launch `/phase-implement` |
| Implementation done, not verified | Launch `/phase-verify` |
| Verification passed | Launch `/phase-x-finalize` |
| All phases complete | Suggest `/topic-pr` |
| Mid-phase (tasks partially done) | Launch `/phase-implement` for current phase |

**Report your decision before launching:**
```
🔄 Resuming: {topic name}
📋 Current state: {phase and status}
🎯 Launching: {next workflow} — {reason}
```

---

## Notes

- This is the **universal Cascade-native entry point** — handles both new work and resume
- After `/phase-research`, workflow STOPS — user reviews findings and answers questions
- User then manually triggers `/phase-plan` when ready (or runs `/kickoff` again to auto-resume)
- For Traycer-based flow (external handoff specs), use `/traycer-kickoff` instead
- Commits are NOT created here — use `/phase-x-finalize` for that
- When resuming, if the state is ambiguous, ASK the user rather than guessing
