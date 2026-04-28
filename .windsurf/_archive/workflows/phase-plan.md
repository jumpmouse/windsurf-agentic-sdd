---
description: Create implementation plan from research findings — break into phases, define tasks, acceptance criteria
---

# PHASE PLAN

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

Planning phase. Creates a detailed, verifiable implementation plan based on research findings.

Use @project-planner mindset throughout.

## Inputs

- **TOPIC_SLUG**: Topic folder name
- **RESEARCH_FINDINGS**: Research document or findings from `/phase-research`

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file — you MUST follow ALL its behaviors and rules throughout this entire workflow:
- Read `.windsurf/skills/project-planner/SKILL.md`

Do NOT proceed to Step 1 until you have read and understood the skill file.

### 1. Read Research Findings

// turbo
Read:
- Research findings document (from `/phase-research` output or topic research files)
- `docs/{TOPIC_SLUG}/PLAN.md` — existing plan state
- Any user answers to research questions

### 2. Create Implementation Plan

Based on the research, create a plan following these principles:

1. **Break into atomic tasks** — Each task modifies specific files for a specific reason
2. **Order by dependency** — Infrastructure → Core → UI → Tests
3. **Define acceptance criteria** — For each task, what "done" means
4. **List files per task** — Exact files that will be modified
5. **Specify the change** — What will change in each file and why
6. **Estimate size** — XS/S/M/L per task

Plan format:
```markdown
## Implementation Plan

### Phase N — {Name}

**Goal:** {One-line goal}
**Depends on:** {previous phase or "Research (done)"}

- [ ] Task 1: {Specific, verifiable action}
  - **Files:** {list}
  - **Change:** {what and why}
  - **Acceptance:** {how to verify}
  - **Size:** {XS/S/M/L}

**Done when:** {Explicit completion criteria}
```

### 3. Estimation Summary

Provide total estimation:

```markdown
## Estimation

| Phase | Size | Duration |
|-------|------|----------|
| Phase 1 | S | ~30 min |
| Phase 2 | M | ~1-2 hours |
| **Total** | | **~X hours** |
```

### 4. Identify Risks

```markdown
## Risks

- {Risk 1 — what could go wrong, mitigation}
- {Risk 2 — what could go wrong, mitigation}
```

### 5. Quality Gate

Before reporting, verify:
- [ ] Every research finding addressed in the plan
- [ ] Tasks are atomic and verifiable
- [ ] No task is vague — each specifies exact files and changes
- [ ] Acceptance criteria are concrete (searchable/testable)
- [ ] Dependency order is correct
- [ ] Estimation provided

### 6. Report

```
✅ Plan Created

📋 Problem: {brief problem statement}

📊 Plan:
- Phases: {count}
- Total tasks: {count}
- Estimated duration: {total}
- Risks: {count}

🎯 Next: /phase-plan-verify to cross-check plan against code
```

---

## Notes

- This workflow STOPS after reporting — user reviews plan, then runs `/phase-plan-verify`
- Do NOT start implementation in this workflow
- If research findings are insufficient, say so — user may need to re-run `/phase-research`
- Update `docs/{TOPIC_SLUG}/PLAN.md` with the new plan
- Commits are NOT created here
