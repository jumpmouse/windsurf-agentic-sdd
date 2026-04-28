---
description: Verify implementation against plan, acceptance criteria, and check for regressions
---

# PHASE VERIFY

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

Implementation verification phase. Verifies the entire implementation against the plan and research findings. Catches regressions and missed acceptance criteria.

Use @verification-fixer mindset throughout.

## Inputs

- **TOPIC_SLUG**: Topic folder name
- **PHASE_NUMBER** (optional): Specific phase to verify (default: most recently implemented phase)

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file — you MUST follow ALL its behaviors and rules throughout this entire workflow:
- Read `.windsurf/skills/verification-fixer/SKILL.md`

Do NOT proceed to Step 1 until you have read and understood the skill file.

### 1. Read Plan and Implementation

// turbo
Read:
- `docs/{TOPIC_SLUG}/PLAN.md` — the plan with acceptance criteria
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — what was implemented
- Latest `SESSION-CHANGES-*.md` — files modified
- Research findings — original investigation context

### 2. Acceptance Criteria Check

For each task's acceptance criteria:
1. **Search** — Can the expected code/behavior be found in the codebase?
2. **Trace** — Does the full execution path work as intended?
3. **Test** — Does it handle edge cases identified in research?

```markdown
## Acceptance Criteria Check
- [x] AC1: {description} — ✅ Verified in {file}
- [x] AC2: {description} — ✅ Verified in {file}
- [ ] AC3: {description} — ⚠️ {issue found}
```

### 3. Side Effect Check

- Re-trace all call sites of modified functions
- Verify consumers still work correctly
- Check that no unrelated functionality would break
- Verify build passes clean

### 4. Fix Issues

If any acceptance criteria not met or regressions found:
1. Categorize by severity (Critical / Major / Minor)
2. Fix Critical issues immediately
3. Fix Major issues next
4. Report Minor issues for user decision

Track fixes:
```markdown
### Verification Fixes
- [x] 🔴 Critical: {description} — Fixed in {file}
- [x] 🟡 Major: {description} — Fixed in {file}
- [ ] 🔵 Minor: {description} — Deferred / Fixed
```

### 5. Quality Gate

- [ ] All acceptance criteria met
- [ ] No regressions in related areas
- [ ] Build passes clean
- [ ] All side effects verified as safe
- [ ] All Critical and Major issues resolved

### 6. Report

```
✅ Verification Complete

📋 Phase: {phase number and name}

📊 Results:
- Acceptance criteria: {X}/{Y} met
- Issues found: {count}
- Critical fixed: {X}
- Major fixed: {X}
- Minor: {X} fixed / {X} deferred
- Regressions: none / {list}

🎯 Next:
- /phase-x-finalize to commit and close
- Or additional fixes if issues remain
```

---

## Notes

- This workflow STOPS after reporting — user reviews, then runs `/phase-x-finalize`
- Fix only what the verification reveals — no new features, no refactoring
- If verification reveals major gaps, recommend re-running `/phase-implement`
- If unable to fix after 3 attempts, stop and report the issue
- Commits are NOT created here — use `/phase-x-finalize` for that
- For Traycer-based flow, use `/traycer-phase-verify` instead
