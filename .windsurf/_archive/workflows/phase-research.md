---
description: Deep investigation of a problem or feature — map all code paths, dependencies, edge cases, and produce findings with questions
---

# PHASE RESEARCH

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

Deep investigation phase. Maps ALL aspects of the problem before any planning or implementation begins.

Use @deep-investigator mindset throughout.

## Inputs

- **PROBLEM**: The problem description, feature request, or bug report
- **TOPIC_SLUG** (optional): Topic folder name

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file — you MUST follow ALL its behaviors and rules throughout this entire workflow:
- Read `.windsurf/skills/deep-investigator/SKILL.md`

Do NOT proceed to Step 1 until you have read and understood the skill file.

### 1. Understand the Problem

Read the problem statement carefully. Identify:
- What is being asked?
- What area of the codebase does this touch?
- Is this a bug fix, new feature, refactoring, or investigation?

### 2. Deep Investigation

Investigate the problem end-to-end. This is NOT a quick scan. This is exhaustive:

1. **Identify all entry points** — Where does the affected code start? (API endpoint, UI event, service call, route)
2. **Trace every code path** — Follow execution from entry to final output. Note every transformation, condition, branch
3. **Map all affected files** — List every file that touches this feature or will be affected by changes
4. **Map all call sites** — For every function/method that may need modification, find ALL places it's called
5. **Understand existing patterns** — How does the codebase handle similar features? What conventions exist?
6. **Identify edge cases** — Null values, empty arrays, boundary conditions, error states
7. **Identify dependencies** — What other features depend on the code you'll change?
8. **Check for side effects** — Will changing X break Y? Map the blast radius
9. **Read related tests** — What test coverage exists? What's missing?
10. **Check recent git history** — Was this area recently changed? By whom? Why?

### 3. Document Findings

Create a structured investigation summary:

```markdown
## Investigation Summary

### Problem Statement
{What the problem is, in precise terms}

### Affected Components
{List every file, service, component involved}

### Current Behavior
{How the code works today — traced end-to-end}

### Root Cause (if bug)
{The actual root cause, not symptoms}

### Dependencies & Side Effects
{What else could break, what depends on this code}

### Existing Patterns
{How similar things are done in the codebase}

### Edge Cases
{All edge cases identified}

### Risks
{What could go wrong with any change here}
```

### 4. Formulate Questions

List any questions that need user input before planning:

```markdown
## Questions for User

1. {Question about scope, priority, or approach}
2. {Question about design decision}
3. {Question about edge case handling}
```

If no questions — state that research is complete and ready for planning.

### 5. Quality Gate

Before reporting, verify:
- [ ] Every affected file identified and read
- [ ] All call sites of key functions traced
- [ ] Existing patterns understood
- [ ] Dependencies and side effects mapped
- [ ] Edge cases documented
- [ ] No assumptions made — everything verified through actual code

**If any of these are not met, continue investigating. Do not stop.**

### 6. Report

```
✅ Research Complete

📋 Problem: {brief problem statement}

🔍 Findings:
- Components affected: {count}
- Files mapped: {count}
- Edge cases identified: {count}
- Risks: {count}

❓ Questions: {count} (review above)

🎯 Next: User reviews findings → /phase-plan
```

---

## Notes

- This workflow STOPS after reporting — user reviews findings and answers questions
- Do NOT start planning or implementation in this workflow
- If the problem is larger than expected, say so — user decides whether to split
- Commits are NOT created here — this is pure investigation
- For Traycer-based flow, use `/traycer-kickoff` instead
