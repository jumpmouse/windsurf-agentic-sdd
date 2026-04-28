---
description: Verify topic ticket implementation against acceptance criteria and check for regressions
---

# TOPIC VERIFY — Ticket Verification

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

Verifies ticket implementation against the ticket spec's acceptance criteria,
guardrails, and checks for regressions. Persists a **Verification Report** file
in `docs/{topic-slug}/reports/` for traceability.

Use @verification-fixer mindset throughout.

## Inputs

- **TICKET_ID**: e.g. `T-04`
- **TOPIC_SLUG** (optional): defaults to auto-detect from current branch /
  ticket ID (most recently modified `docs/{slug}/tickets/{TICKET_ID}__*.md`)

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file:
- Read `.windsurf/skills/verification-fixer/SKILL.md`

### 1. Read Ticket Spec and Implementation

// turbo

Read:
- `docs/{TOPIC_SLUG}/tickets/{TICKET_ID}__*.md` — acceptance criteria, guardrails, detailed steps
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — what was implemented (if updated)
- Any prior verification report for this ticket (if re-running)
- Implementation report from conversation (from `/topic-implement`)
- Any research findings from conversation (from `/topic-research`)

### 2. Acceptance Criteria Check

For each acceptance criterion in the ticket spec:

1. **Search** — Can the expected code/behavior be found in the codebase?
2. **Trace** — Does the full execution path work as intended?
3. **Verify** — Read the actual file and confirm the change is correct

Keep a per-AC check list with evidence (file:line, grep output, or command
output) — this feeds §8 below.

### 3. Guardrails Check

For each guardrail in the ticket spec:
- Verify the constraint was NOT violated
- Check that out-of-scope changes were NOT made

Keep a per-guardrail check list with evidence — feeds §8.

### 4. Side Effect Check

- Re-trace all call sites of modified functions/components
- Verify consumers still work correctly
- Check that no unrelated functionality would break
- Verify build and tests pass clean:

Run the project's build and test commands (defined by the host project —
typically captured as `{BUILD_COMMAND}` and `{TEST_COMMAND}` in
`docs/LOCAL_SETUP.md` or in a project-local override of this workflow):

```bash
{BUILD_COMMAND}
{TEST_COMMAND}
```

Record exit codes, total pass/fail/skipped, and hash/time — feeds §8.

### 5. Regression Spot-Checks

Targeted inspection of areas the change could plausibly break. For each area:
- Identify the relevant file
- Read the relevant region
- Confirm behavior is preserved

Record findings — feeds §8.

### 6. Fix Issues

If any acceptance criteria not met, guardrails violated, or regressions found:

1. Categorize by severity:
   - 🔴 Critical — must fix
   - 🟡 Major — should fix
   - 🔵 Minor — fix if cheap; otherwise document and defer
   - 💡 Suggestion — non-blocking
2. Fix Critical issues immediately
3. Fix Major issues next
4. Report Minor/Suggestion issues

Track each fix with: the change applied, the verification run, any side-effect
re-check — feeds §8.

### 7. Quality Gate

- [ ] All acceptance criteria met
- [ ] All guardrails respected
- [ ] No regressions in related areas
- [ ] `{BUILD_COMMAND}` passes clean (exit 0)
- [ ] `{TEST_COMMAND}` passes at baseline
- [ ] All 🔴 Critical issues resolved
- [ ] All 🟡 Major issues resolved or deferred with follow-up ticket ref
- [ ] Inline shim markers (if any) map 1:1 to their inventory file

### 8. Write Verification Report

Create `docs/{TOPIC_SLUG}/reports/{TICKET_ID}__{short_slug}__VERIFICATION_REPORT.md`
using `.windsurf/_templates/verification-report-template.md`.

The report consolidates everything from steps §2–§7:

- Summary + verdict
- AC table (from §2)
- Guardrails table (from §3)
- Side-effect results (from §4: build / test / regression spot-checks)
- Issues found & fix log (from §6)
- Quality gate checklist (from §7)
- Deferred / carry-over items
- Final verdict block

This file is the durable artifact of verification — `/topic-close` consumes it
to build the Completion Report. Use the **same file naming convention** as the
completion report but with `__VERIFICATION_REPORT.md` suffix.

### 9. Report

```
✅ Verification Complete — {TICKET_ID}

📊 Results:
- Acceptance criteria: {X}/{Y} met
- Guardrails: {X}/{Y} respected
- Issues found: {count}
- Critical fixed: {X}
- Major fixed: {X}
- Minor: {X} fixed / {X} deferred
- Regressions: none / {list}
- Build: ✅
- Tests: {X} passed, {Y} skipped

📄 Verification Report:
  docs/{TOPIC_SLUG}/reports/{TICKET_ID}__{short_slug}__VERIFICATION_REPORT.md

🎯 Next: /topic-close {TICKET_ID}
```

---

## Notes

- This workflow **STOPS** after reporting — user reviews, then runs `/topic-close`
- Fix only what the verification reveals — no new features, no refactoring
- If verification reveals major gaps, recommend re-running `/topic-implement`
- If unable to fix after 3 attempts, stop and report the issue
- Commits are NOT created here — use `/topic-close` for that
- The Verification Report is **separate from the Completion Report**:
  `__VERIFICATION_REPORT.md` documents the verification pass with fixes applied,
  `__REPORT.md` (written by `/topic-close`) is the ticket-completion narrative
