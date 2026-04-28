---
description: "[Traycer] Implement phase from Traycer handoff — read spec, gather context, implement tasks"
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

The core implementation workflow. Reads a Traycer handoff plan (or PLAN.md phase), gathers all context, and implements tasks following the spec precisely.

Use @spec-implementer mindset throughout.

## Inputs

- **HANDOFF_PATH** (optional): Path to Traycer handoff document (e.g., `docs/{TOPIC_SLUG}/plan/handoff/Phase_4C_Handoff.md`)
- **PHASE_NUMBER** (optional): Phase number to implement from PLAN.md (e.g., `3`)
- If neither provided, auto-detect from PLAN.md (first incomplete phase)

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file — you MUST follow ALL its behaviors and rules throughout this entire workflow:
- Read `.windsurf/skills/spec-implementer/SKILL.md`

Do NOT proceed to Step 1 until you have read and understood the skill file.

### 1. Gather Context

// turbo
Read topic documentation:
- `docs/{TOPIC_SLUG}/README.md` — overview, Linear issues
- `docs/{TOPIC_SLUG}/PLAN.md` — find target phase
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — what's been done
- Latest `SESSION-CHANGES-*.md` — recent state

If HANDOFF_PATH provided:
// turbo
- Read the handoff document end-to-end
- Read all referenced specs (API contracts, data models, etc.)

### 2. Parse Requirements

Extract from handoff/phase:
- **Goal**: What this phase achieves
- **Acceptance Criteria**: Exact conditions for "done"
- **Files to Modify**: List of target files with expected changes
- **Dependencies**: What must exist from previous phases
- **References**: Related specs, PRDs, or existing code patterns

Report what was found:
```
📋 Phase {N}: {Title}

Goal: {goal}
Files: {count} files to modify
Acceptance Criteria: {count} items
Dependencies: {list or "none"}

Proceeding with implementation...
```

### 3. Pre-Implementation Check

Before writing any code:
1. Verify referenced files/components exist
2. Understand current state of code being modified (trace call sites if modifying functions)
3. Confirm approach aligns with existing patterns
4. Flag any conflicts between spec and reality — ASK before proceeding

### 4. Implement

For each task in the phase (sequentially):

1. **Read** the specific task requirements
2. **Investigate** the target code area
3. **Implement** the change — minimal, focused, spec-compliant
4. **Verify** — build passes, no type errors
5. **Mark** task complete in PLAN.md

Follow the three-tier boundary system:
- ✅ **Always do**: Follow spec, update docs, verify builds
- ⚠️ **Ask first**: Any deviation from spec, new dependencies, schema changes
- 🚫 **Never do**: Scope creep, skip acceptance criteria, commit to dev/main

### 5. Self-Verify

After all tasks:
- Compare implementation against ALL acceptance criteria
- List any criteria not fully met
- Check for unintended side effects
- Run local build: `npx nx build` (FE) or `dotnet build` (BE)

```markdown
## Acceptance Criteria Check
- [x] AC1: {description} — ✅ Implemented in {file}
- [x] AC2: {description} — ✅ Implemented in {file}
- [ ] AC3: {description} — ⚠️ {issue or question}
```

### 6. Update Documentation

- `PLAN.md` — mark completed tasks
- `CHANGELOG.md` — add entries for work done
- `SESSION-CHANGES-*.md` — list files changed
- If deviations from spec: document in CHANGELOG with rationale

### 7. Report Completion

```
✅ Phase {N} implementation complete

📊 Results:
- Tasks completed: {X}/{Y}
- Files modified: {list}
- Acceptance criteria met: {X}/{Y}

⚠️ Issues (if any):
- {any unresolved items}

💡 Suggestions (out of scope):
- {any improvements noticed but not implemented}

🎯 Next: Traycer verification → then /traycer-phase-verify if issues found
```

---

## Notes

- Do NOT improvise beyond the spec — extras go in "Suggestions"
- If a task is unclear, ASK before implementing
- If build fails after a change, fix before proceeding to next task
- Commit frequently with `[{LINEAR_ISSUE_ID}]` prefix (do NOT push)
