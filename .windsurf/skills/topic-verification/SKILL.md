---
name: topic-verification
description: Verify ticket implementation against the ticket spec's Acceptance Criteria and Guardrails. Categorize findings by severity, fix Critical and Major issues, persist a Verification Report.
---

# Topic Verification Skill

## Persona

**MANDATORY first action:** Load the Verifier persona by reading `.windsurf/personas/verifier.md`. Adopt it for the duration of this skill.

## Inputs

- **TICKET_ID** (required) — e.g. `T-04`.
- **TOPIC_SLUG** (optional) — auto-detect from current branch / ticket ID (most recently modified `docs/{slug}/tickets/{TICKET_ID}__*.md`).

## Output

- `docs/{TOPIC_SLUG}/reports/{TICKET_ID}__{short_slug}__VERIFICATION_REPORT.md` using `.windsurf/_templates/verification-report-template.md`.
- Code fixes for any issues found (Critical first, then Major).
- No commits.

## Procedure

### 1. Read ticket spec and implementation

Read:
- `docs/{TOPIC_SLUG}/tickets/{TICKET_ID}__*.md` — Acceptance Criteria, Guardrails, Detailed Steps.
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — what was implemented (if updated).
- Any prior verification report for this ticket (if re-running).
- Implementation report from conversation (if still in context).
- Research findings (if `/topic-research` was run).

### 2. Acceptance Criteria check

For each AC in the ticket spec:

1. **Search** — Can the expected code/behavior be found in the codebase?
2. **Trace** — Does the full execution path work as intended?
3. **Verify** — Read the actual file and confirm the change is correct.

Keep a per-AC check list with evidence (file:line, grep output, command output) — feeds §8.

### 3. Guardrails check

For each guardrail:
- Verify the constraint was NOT violated.
- Check that out-of-scope changes were NOT made.

Keep a per-guardrail check list with evidence — feeds §8.

### 4. Side-effect check

- Re-trace all call sites of modified functions / components.
- Verify consumers still work correctly.
- Check that no unrelated functionality would break.
- Verify build + tests pass clean.

Run the project's build and test commands (defined as `{BUILD_COMMAND}` and `{TEST_COMMAND}` in the host project's root `AGENTS.md`):

```bash
{BUILD_COMMAND}
{TEST_COMMAND}
```

Record exit codes, total pass/fail/skipped, and hash/time — feeds §8.

### 5. Regression spot-checks

Targeted inspection of areas the change could plausibly break. For each area:
- Identify the relevant file.
- Read the relevant region.
- Confirm behavior is preserved.

Record findings — feeds §8.

### 6. Fix issues

If any AC unmet, guardrails violated, or regressions found:

1. Categorize by severity (Verifier persona's discipline):
   - 🔴 Critical — must fix
   - 🟡 Major — should fix
   - 🔵 Minor — fix if cheap; otherwise document and defer
   - 💡 Suggestion — non-blocking
2. Fix Critical issues immediately.
3. Fix Major issues next.
4. Report Minor / Suggestion issues.

Track each fix with: change applied, verification run, side-effect re-check — feeds §8.

### 7. Quality Gate

- [ ] All Acceptance Criteria met.
- [ ] All Guardrails respected.
- [ ] No regressions in related areas.
- [ ] `{BUILD_COMMAND}` passes clean (exit 0).
- [ ] `{TEST_COMMAND}` passes at baseline.
- [ ] All 🔴 Critical issues resolved.
- [ ] All 🟡 Major issues resolved or deferred with follow-up ticket ref.

### 8. Write Verification Report

Create `docs/{TOPIC_SLUG}/reports/{TICKET_ID}__{short_slug}__VERIFICATION_REPORT.md` using `.windsurf/_templates/verification-report-template.md`. Consolidates §2–§7:

- Summary + verdict
- AC table (from §2)
- Guardrails table (from §3)
- Side-effect results (from §4: build / test / regression spot-checks)
- Issues found & fix log (from §6)
- Quality gate checklist (from §7)
- Deferred / carry-over items
- Final verdict block

This file is the durable artifact `/topic-close` consumes to build the Completion Report. **Distinct** from the Completion Report (which has `__REPORT.md` suffix).

## Final Report

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

📄 Verification Report: docs/{TOPIC_SLUG}/reports/{TICKET_ID}__{short_slug}__VERIFICATION_REPORT.md

🎯 Next: /topic-close {TICKET_ID}
```

Then **STOP**. Do not commit — that's `/topic-close`'s job.

## Notes

- Fix only what verification reveals — no new features, no refactoring.
- If verification reveals major gaps, recommend re-running `/topic-implement`.
- 3-attempt rule applies: if you can't fix an issue after 3 attempts, stop and report.
