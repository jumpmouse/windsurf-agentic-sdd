---
description: Execute verified implementation plan — implement tasks one by one with discipline and verification
---

# PHASE IMPLEMENT

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

Implementation phase. Executes the verified plan task by task. No shortcuts, no assumptions.

Use @spec-implementer mindset throughout.

## Inputs

- **TOPIC_SLUG**: Topic folder name
- **PHASE_NUMBER** (optional): Specific phase to implement (default: first incomplete phase)

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file — you MUST follow ALL its behaviors and rules throughout this entire workflow:
- Read `.windsurf/skills/spec-implementer/SKILL.md`

Do NOT proceed to Step 1 until you have read and understood the skill file.

### 1. Read Plan

// turbo
Read:
- `docs/{TOPIC_SLUG}/PLAN.md` — find the phase to implement
- Research findings — understand the context behind the plan
- Any referenced specs, API contracts, data models

### 2. Pre-Implementation Check

Before writing any code:
1. Verify you're on the correct git branch
2. Verify referenced files exist and match expected state
3. Confirm no uncommitted changes from other work

### 3. Execute Tasks

For each task in the plan (sequentially, never skip):

1. **Read** — Re-read the task requirements and target files
2. **Investigate** — If anything is unclear about the target code, investigate before changing
3. **Implement** — Make the change, minimal and focused
4. **Verify locally** — Does the change work? Build passes? Types check?
5. **Check side effects** — Re-read all call sites. Did this break anything?
6. **Mark complete** — Update PLAN.md checkbox

**Three-tier boundaries:**
- ✅ **Always do**: Follow the plan, verify each change, update docs
- ⚠️ **Ask first**: Any deviation from plan, new dependencies, schema changes
- 🚫 **Never do**: Scope creep, skip verification, assume a change is safe without checking

### 4. Ongoing Verification

After every 2-3 tasks, do a mini-verification:
- Build still passes?
- Changes so far are consistent with each other?
- No unintended interactions between changes?

### 5. Quality Gate

After all tasks:
- [ ] Every plan task completed
- [ ] Build passes
- [ ] No TypeScript/C# errors
- [ ] Every change verified against its acceptance criteria
- [ ] PLAN.md updated with completed checkboxes

### 6. Report

```
✅ Implementation Complete

📋 Phase: {phase number and name}

📊 Results:
- Tasks completed: {X}/{Y}
- Files modified: {list}
- Acceptance criteria: {X}/{Y} met

⚠️ Issues (if any):
- {unresolved items}

💡 Suggestions (out of scope):
- {improvements noticed but not implemented}

🎯 Next: /phase-verify to verify implementation
```

---

## Notes

- This workflow STOPS after reporting — user reviews, then runs `/phase-verify`
- Do NOT improvise beyond the plan — extras go in "Suggestions"
- If a task is unclear, ASK before implementing
- If build fails after a change, fix before proceeding to next task
- Commits are NOT created here — use `/phase-x-finalize` for that
- For Traycer-based flow, use `/traycer-phase-implement` instead
