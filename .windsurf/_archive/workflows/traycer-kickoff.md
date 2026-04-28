---
description: "[Traycer] Quick-start a phase — combines session context + Traycer handoff implementation in one command"
---

# TRAYCER KICKOFF — Traycer Development Flow

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

Universal entry point for the Traycer workflow. Gathers context via `/traycer-session-start`, detects pending artifacts, and routes to the correct next action.

## Inputs (all optional — auto-detected if not provided)

- **TOPIC_SLUG**: Topic folder name (auto-detect from WORK_LOG.md)
- **HANDOFF_PATH**: Traycer handoff document path (auto-detect from `plan/handoff/`)
- **PHASE_NUMBER**: Phase to implement (auto-detect from PLAN.md)

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file — you MUST follow ALL its behaviors and rules throughout this entire workflow:
- Read `.windsurf/skills/spec-implementer/SKILL.md`

Do NOT proceed to Step 1 until you have read and understood the skill file.

### 1. Gather Session Context

// turbo
Execute `/traycer-session-start` — this reads all root docs, identifies the current milestone/topic/phase, checks for Traycer artifacts, and reports status.

**Do NOT duplicate what /traycer-session-start already does.** Use its output as your context.

### 2. Route Based on Artifacts

Based on traycer-session-start findings:

| Traycer Artifact Found | Action |
|------------------------|--------|
| **Verification report** pending | Switch to `/traycer-phase-verify` (fix issues, not implement) |
| **Handoff** pending | Continue to Step 3 (implement from handoff) |
| **Neither** | Continue to Step 3 (implement from PLAN.md) |

### 3. Read Phase Requirements

If handoff exists:
// turbo
- Read the handoff document end-to-end
- Read all referenced specs (API contracts, data models, etc.)

If no handoff:
// turbo
- Read phase tasks from PLAN.md

Extract:
- **Goal**: What this phase achieves
- **Acceptance Criteria**: Conditions for "done"
- **Files to Modify**: Target files
- **Dependencies**: Previous phase requirements

### 4. Status Report

```
📍 Traycer Kickoff — Phase {N}: {Title}

🗂️ Topic: {topic_slug}
🎫 Linear: {LINEAR_ID}
🔀 Branch: {current branch}

📋 Phase Goal: {goal}
📝 Acceptance Criteria: {count} items
📁 Files to modify: {count}

Implementing...
```

### 5. Pre-Implementation Check

Before writing any code:
1. Verify referenced files/components exist
2. Understand current state of code being modified
3. Confirm approach aligns with existing patterns
4. Flag conflicts between spec and reality — ASK before proceeding

### 6. Implement

For each task (sequentially):
1. **Read** the specific task requirements
2. **Investigate** the target code area
3. **Implement** — minimal, focused, spec-compliant
4. **Verify** — build passes, no type errors
5. **Mark** task complete in PLAN.md

Three-tier boundaries:
- ✅ **Always**: Follow spec, update docs, verify builds
- ⚠️ **Ask first**: Any deviation from spec, new dependencies, schema changes
- 🚫 **Never**: Scope creep, skip acceptance criteria, commit to dev/main

### 7. Self-Verify

After all tasks:
```markdown
## Acceptance Criteria Check
- [x] AC1: {description} — ✅ Implemented in {file}
- [x] AC2: {description} — ✅ Implemented in {file}
- [ ] AC3: {description} — ⚠️ {issue}
```

### 8. Update Documentation

- `PLAN.md` — mark completed tasks
- `CHANGELOG.md` — add entries for work done
- Create/update `SESSION-CHANGES-{DATE}.md`

### 9. Report

```
✅ Phase {N} implementation complete

📊 Results:
- Tasks: {X}/{Y} completed
- Files modified: {list}
- Acceptance criteria: {X}/{Y} met

⚠️ Issues (if any):
- {unresolved items}

💡 Suggestions (out of scope):
- {improvements noticed}

🎯 Next:
- Traycer verification → /traycer-phase-verify if issues
- Or → /phase-x-finalize if clean
```

---

## Notes

- This is the **universal Traycer entry point** — gathers context + routes + implements
- Delegates context gathering to `/traycer-session-start` — never duplicate
- If verification report found, auto-switches to `/traycer-phase-verify`
- For Cascade-native flow (no Traycer), use `/kickoff` instead
- Commits are NOT created here — use `/phase-x-finalize` for that
