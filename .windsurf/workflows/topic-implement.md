---
description: Implement an topic ticket — execute ticket spec steps with discipline and verification
---

# TOPIC IMPLEMENT — Ticket Implementation

## 🌐 Project Override Hook

Before executing this workflow, check whether the host project provides an override.

**Path** (in the host project's workspace root, NOT this global repo):
`.windsurf/overrides/workflows/<same-filename-as-this-file>.md`

If present, apply its mode (frontmatter `mode:` OR first heading `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — run ONLY the override; ignore the steps below.
- **EXTEND** — run the steps below as written, then run the override as a final phase (or at the position it specifies).
- **MODIFY** — run the steps below, with the steps the override declares substituted / inserted at the matching positions.

No override → proceed as written. Ambiguous mode → **STOP and ask the user**.

---

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

Core implementation workflow. Reads the ticket spec as the plan and executes its steps with discipline.

Use @spec-implementer mindset throughout.

## Inputs

- **TICKET_ID**: e.g. `T-04`

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file:
- Read `.windsurf/skills/spec-implementer/SKILL.md`

### 1. Read Ticket Spec

// turbo
Read end-to-end:
- `docs/{TOPIC_SLUG}/tickets/{TICKET_ID}__*.md` — this IS the implementation plan
- Dependency tickets' reports if needed
- Research findings from conversation (if `/topic-research` was run)

Extract:
- **Detailed Steps** — ordered list of implementation tasks
- **Acceptance Criteria** — conditions for "done"
- **Guardrails** — what NOT to do
- **Files affected** — every file mentioned

### 2. Pre-Implementation Check

Before writing any code:
1. Verify you are on the correct feature branch for this topic (the host project's convention applies; if unclear, ASK)
2. Verify referenced files exist and match expected state
3. Check for uncommitted changes from other work
4. If research was done, check that findings don't conflict with ticket steps

Flag any conflicts between spec and reality — **ASK before proceeding**.

### 3. Execute Steps

For each step in the ticket's "Detailed Steps" section (sequentially, never skip):

1. **Read** — Re-read the step requirements and target files
2. **Investigate** — If anything is unclear about the target code, investigate before changing
3. **Implement** — Make the change, minimal and focused, exactly as the ticket describes
4. **Verify** — Does the change compile? Types check? No regressions?
5. **Check side effects** — Re-read all call sites. Did this break anything?
6. **Track** — Update the todo list with progress

**Three-tier boundaries:**
- ✅ **Always do**: Follow ticket spec, verify each change, track progress
- ⚠️ **Ask first**: Any deviation from spec, new dependencies, changes to files not in the ticket
- 🚫 **Never do**: Scope creep, skip verification, assume a change is safe without checking

### 4. Ongoing Verification

After every 2-3 steps, run the project's build command (defined by the host
project — typically captured as `{BUILD_COMMAND}` in `docs/LOCAL_SETUP.md` or
in a project-local override of this workflow):

```bash
{BUILD_COMMAND}
```

If build fails, fix before proceeding.

### 5. Self-Verify Against Acceptance Criteria

After all steps complete:

```markdown
## Acceptance Criteria Check
- [x] AC1: {description} — ✅ Verified in {file}
- [x] AC2: {description} — ✅ Verified in {file}
- [ ] AC3: {description} — ⚠️ {issue or question}
```

Also run the project's full test suite (defined by the host project as
`{TEST_COMMAND}`):

```bash
{TEST_COMMAND}
```

### 6. Report

```
✅ Implementation Complete — {TICKET_ID}

📊 Results:
- Steps completed: {X}/{Y}
- Files modified: {count}
- Acceptance criteria: {X}/{Y} met
- Build: ✅ / ❌
- Tests: {X} passed, {Y} skipped

⚠️ Issues (if any):
- {unresolved items}

💡 Suggestions (out of scope):
- {improvements noticed but not implemented}

🎯 Next: User reviews → /topic-verify {TICKET_ID}
```

---

## Notes

- This workflow **STOPS** after reporting — user reviews implementation
- The ticket spec IS the plan — follow its "Detailed Steps" section exactly
- Do NOT improvise beyond the spec — extras go in "Suggestions"
- If a step is unclear, ASK before implementing
- If build fails after a change, fix before proceeding to next step
- Guardrails in the ticket spec are hard constraints — never violate them
- Commits are NOT created here — use `/topic-close` for that
