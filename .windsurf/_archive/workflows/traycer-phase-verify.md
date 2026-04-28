---
description: "[Traycer] Fix issues from Traycer verification report — implement fixes by severity"
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

Read a Traycer verification report and implement targeted fixes. Prioritize by severity. Repeat until clean.

Use @verification-fixer mindset throughout.

## Inputs

- **VERIFICATION_PATH** (optional): Path to Traycer verification document (e.g., `docs/{TOPIC_SLUG}/plan/verification/Verification_Phase_4C.md`)
- If not provided, auto-detect latest verification file in `docs/{TOPIC_SLUG}/plan/verification/`

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file — you MUST follow ALL its behaviors and rules throughout this entire workflow:
- Read `.windsurf/skills/verification-fixer/SKILL.md`

Do NOT proceed to Step 1 until you have read and understood the skill file.

### 1. Locate Verification Report

// turbo
Find and read the verification document:
- Check `docs/{TOPIC_SLUG}/plan/verification/` for the latest file
- Read the full report end-to-end
- Also read `PLAN.md` to understand the phase context

### 2. Parse Issues

Extract all verification comments and categorize:

```
📋 Verification Report: {filename}

🔴 Critical: {count} — blocks core functionality
🟡 Major: {count} — significant issues
🔵 Minor: {count} — polish items
⚪ Outdated: {count} — skip

Total fixes needed: {critical + major + minor}
```

Identify dependencies between issues (fix A before B).

### 3. Fix by Severity

Process in strict order: **Critical → Major → Minor**

For each issue:
1. **Read** the verification comment and affected files
2. **Investigate** the code — understand context, trace call sites
3. **Fix** — implement only what the report describes
4. **Verify** — confirm fix works, no regressions
5. **Track** — mark as resolved

After each fix, verify the build still passes.

### 4. Update Documentation

- `PLAN.md` — add verification fixes section under the phase:
  ```markdown
  #### Verification Fixes (Round {N})
  - [x] 🔴 {Critical fix description}
  - [x] 🟡 {Major fix description}
  - [x] 🔵 {Minor fix description}
  ```
- `CHANGELOG.md` — add entries for fixes
- `SESSION-CHANGES-*.md` — list files changed

### 5. Report Completion

```
✅ Verification fixes complete (Round {N})

📊 Results:
- Critical fixed: {X}/{Y}
- Major fixed: {X}/{Y}
- Minor fixed: {X}/{Y}
- Outdated skipped: {X}
- Files modified: {list}

🎯 Next: Traycer re-verification
  - If clean → /phase-finalize
  - If issues remain → /traycer-phase-verify again
```

---

## Notes

- Fix ONLY what the report says — no new features, no refactoring
- If a fix requires changing something that affects other phases, ASK first
- If unable to fix after 3 attempts, stop and report the issue
- Outdated comments: mark as skipped with reason
- Minor issues: fix if straightforward, defer if complex (note in report)
