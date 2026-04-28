---
description: Finalize phase work — review, fix, commit, and close the phase
---

# PHASE FINALIZE

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

Final quality gate before closing a phase. Reviews all changes, commits meaningful units, and updates documentation.

Use @code-reviewer mindset for the review step.

## Inputs

- **PHASE_NUMBER** (optional): Phase to finalize (default: current active phase from PLAN.md)

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file — you MUST follow ALL its behaviors and rules throughout this entire workflow:
- Read `.windsurf/skills/code-reviewer/SKILL.md`

Do NOT proceed to Step 1 until you have read and understood the skill file.

### 1. Gather Phase Context

// turbo
Read:
- `docs/{TOPIC_SLUG}/PLAN.md` — identify phase tasks and status
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — work done in this phase
- Latest `SESSION-CHANGES-*.md` — files modified

### 2. Code Review

Use @code-reviewer to perform a thorough review of all changes in this phase.

Check each modified file for:
- **Correctness** — Does it do what the spec/plan says?
- **Side effects** — Does it break anything outside the phase scope?
- **Code quality** — Follows existing patterns, no dead code, proper error handling
- **Type safety** — No `any` types, proper interfaces, null checks
- **Naming** — Consistent with codebase conventions

If issues found:
1. Fix them immediately (minor)
2. Or report and ask (if scope is unclear)

### 3. Verify Build

// turbo
Run build checks:

**FE** (if FE changes exist):
```bash
cd /Users/milosbr/projects/lot/fe/landOfTales
npx nx build
```

**BE** (if BE changes exist):
```bash
cd /Users/milosbr/projects/lot/be
dotnet build
```

If build fails, fix before proceeding.

### 4. Create Commits

Group changes into meaningful commits. Each commit should be:
- Self-contained and logical
- Prefixed with Linear issue ID: `[LAN-XXX] description`
- Descriptive of what was changed and why

Typical commit grouping:
- Backend changes (if any)
- Frontend component changes
- Frontend service/model changes
- Documentation changes

**Do NOT push.** User decides when to push.

### 5. Update Documentation

- `PLAN.md` — mark phase as complete, check all tasks
- `CHANGELOG.md` — final phase summary entry
- `SESSION-CHANGES-*.md` — complete file list

### 6. Update WORK_LOG.md

In `docs/WORK_LOG.md`, check the phase checkbox under the current topic.

### 7. Report Completion

```
✅ Phase {N} finalized: {Phase Title}

📊 Summary:
- Tasks completed: {X}/{Y}
- Files modified: {count}
- Commits created: {count}
- Build status: ✅ passing

🔀 Git status:
- Branch: {branch_name}
- Commits: {count} unpushed
- Clean working tree: ✅

🎯 Next:
- If more phases remain → /phase-implement for next phase (or /traycer-phase-implement if Traycer)
- If all phases complete → /topic-pr
```

---

## Notes

- This is the ONLY workflow that creates commits
- Never push — user controls when to push
- If review finds issues that need re-verification, note them
- Always verify build passes before committing
