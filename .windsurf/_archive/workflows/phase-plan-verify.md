---
description: Verify implementation plan against research findings, specs, and actual code before implementation
---

# PHASE PLAN VERIFY

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

Plan verification phase. Cross-checks the implementation plan against research findings AND actual code state. Catches gaps before any implementation begins.

Use @verification-fixer mindset throughout.

## Inputs

- **TOPIC_SLUG**: Topic folder name

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file — you MUST follow ALL its behaviors and rules throughout this entire workflow:
- Read `.windsurf/skills/verification-fixer/SKILL.md`

Do NOT proceed to Step 1 until you have read and understood the skill file.

### 1. Read Plan and Research

// turbo
Read:
- `docs/{TOPIC_SLUG}/PLAN.md` — the implementation plan to verify
- Research findings document (from `/phase-research`)
- Any relevant specs, API contracts, data models referenced in the plan

### 2. Verify Completeness

For each research finding, verify it's covered in the plan:
- Does the plan address the root cause?
- Does the plan handle all edge cases found in research?
- Does the plan account for all dependencies?
- Does the plan follow existing patterns identified in research?

Report gaps:
```markdown
## Completeness Check
- [x] Finding 1: {description} — Covered by Task {X}
- [x] Finding 2: {description} — Covered by Task {Y}
- [ ] Finding 3: {description} — ⚠️ NOT covered in plan
```

### 3. Verify Against Code

For each task in the plan:
1. **Re-read the target file** — Is the plan's assumption about current code still accurate?
2. **Check call sites** — Will the planned change break any callers?
3. **Check consumers** — Will downstream code still work?
4. **Check types** — Will TypeScript/C# types align after the change?

Report issues:
```markdown
## Code Verification
- [x] Task 1: {file} — Code matches plan assumptions ✅
- [ ] Task 2: {file} — ⚠️ Plan assumes {X} but code actually does {Y}
```

### 4. Verify Acceptance Criteria

For each acceptance criterion:
- Is it testable/searchable?
- Is it specific enough to verify after implementation?
- Does it actually prove the task is done?

### 5. Fix Plan Issues

If gaps found:
- Add missing tasks
- Reorder tasks if dependencies were wrong
- Add edge case handling tasks
- Fix incorrect assumptions
- Add missing acceptance criteria

Update `docs/{TOPIC_SLUG}/PLAN.md` with corrections.

### 6. Quality Gate

- [ ] Every research finding has a corresponding plan task
- [ ] Every plan task verified against actual current code
- [ ] No call site left unchecked
- [ ] Plan accounts for all side effects
- [ ] Acceptance criteria are achievable and testable
- [ ] No assumptions — everything verified through code reading

**If verification reveals significant new information, recommend going back to `/phase-research` for those areas.**

### 7. Report

```
✅ Plan Verified

📋 Results:
- Research findings covered: {X}/{Y}
- Tasks verified against code: {X}/{Y}
- Issues found and fixed: {count}
- Plan is ready for implementation: ✅ / ⚠️

{If issues were fixed, list them}

🎯 Next: /phase-implement to execute the verified plan
```

---

## Notes

- This workflow STOPS after reporting — user reviews, then runs `/phase-implement`
- Do NOT start implementation in this workflow
- If major issues found, recommend re-running `/phase-research` or `/phase-plan`
- Update PLAN.md if corrections were made
- Commits are NOT created here
