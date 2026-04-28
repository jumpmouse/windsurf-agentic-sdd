# {TICKET_ID}: {Ticket Title} — Verification Report

**Ticket:** {TICKET_ID}
**Topic:** [{topic title}](../README.md)
**Ticket spec:** [{TICKET_ID}](../tickets/{TICKET_FILENAME}.md)
**Date:** {YYYY-MM-DD}
**Branch:** `{branch_name}`
**Verifier mindset:** @code-reviewer
**Outcome:** {✅ PASS | ⚠️ PASS WITH FIXES | ❌ FAIL}

---

## 1. Summary

{2–4 sentence high-level verdict. Mention total AC count met, any Critical/Major
fixes performed during verification, and final build/test status.}

---

## 2. Acceptance Criteria Check

| # | Criterion | Status | Evidence |
|---|-----------|:------:|----------|
| AC1 | {criterion from ticket} | ✅ / ❌ | `file:path:line` or command output |
| AC2 | {criterion} | ✅ / ❌ | {evidence} |
| ... | ... | ... | ... |

**Result:** {X}/{Y} acceptance criteria met.

---

## 3. Guardrails Check

| Guardrail | Status | Notes |
|-----------|:------:|-------|
| {guardrail from ticket} | ✅ / ❌ | {what was verified} |

**Result:** {X}/{Y} guardrails respected.

---

## 4. Side Effect Check

### 4.1 Build

```bash
{command used}
```

| Check | Result |
|-------|--------|
| Exit code | 0 / N |
| Compilation errors | 0 / N — {list if any} |
| New warnings vs baseline | 0 / N — {categorized} |
| Bundle size delta vs baseline | {+/-X KB} |
| Hash / time | `{hash}` / {duration} |

### 4.2 Test

```bash
{command used}
```

| Check | Result |
|-------|--------|
| Total executed | {N} |
| Passed | {N} |
| Failed | {N} (vs baseline 0) |
| Skipped | {N} (vs baseline) |
| New failures introduced | 0 / list |
| New regressions | 0 / list |

### 4.3 Regression Spot-Checks

Targeted checks on areas the change could plausibly break:

- **{area 1}** — {file path} — {what was inspected, result}
- **{area 2}** — {...}
- **{area 3}** — {...}

---

## 5. Issues Found & Fixes Applied

Categorize every finding by severity. Severity scale:

- 🔴 **Critical** — breaks correctness, security, or data integrity. Must be
  fixed before ticket closes.
- 🟡 **Major** — missed acceptance criterion, missed guardrail, or latent bug.
  Should be fixed before ticket closes.
- 🔵 **Minor** — style, naming, missed cleanliness item. Fix if cheap; otherwise
  document and defer.
- 💡 **Suggestion** — non-blocking alternative approach or improvement idea.

### 5.1 Findings

| # | Severity | File:Line | Finding | Status |
|---|:--------:|-----------|---------|:------:|
| 1 | 🔴 / 🟡 / 🔵 / 💡 | `file:...` | {one-sentence description} | Fixed / Deferred / Suggestion |

### 5.2 Fix Log

For each Fixed entry above, detail the fix applied:

**Fix {#}:** {file:line} — {finding}

- **Change applied:** {concrete description, e.g. "Replaced `text-right` with
  `text-end` in `[className]` ternary branch at line X"}
- **Verified by:** {grep / build / test command}
- **Side effects checked:** {what else was inspected}

---

## 6. Quality Gate

- [ ] All acceptance criteria met
- [ ] All guardrails respected
- [ ] No regressions in related areas
- [ ] Build passes (exit 0)
- [ ] Tests pass at baseline
- [ ] All 🔴 Critical issues resolved
- [ ] All 🟡 Major issues resolved or explicitly deferred with a follow-up ticket
- [ ] Inline markers (if any) map 1:1 to inventory entries
- [ ] No undocumented scope changes beyond ticket spec

---

## 7. Deferred / Carry-Over

Items surfaced during verification but explicitly NOT fixed here.

- **{item}** — {why deferred, where it will be handled}

---

## 8. Final Verdict

```
{✅ VERIFICATION PASSED | ⚠️ PASSED WITH FIXES | ❌ VERIFICATION FAILED}

📊 Results:
- Acceptance criteria: {X}/{Y} met
- Guardrails: {X}/{Y} respected
- Issues fixed: {🔴 X} / {🟡 Y} / {🔵 Z}
- Issues deferred: {count} (see §7)
- Build: ✅ / ❌
- Tests: {N} passed, {M} skipped

🎯 Next:
- {/topic-close {TICKET_ID}} — if PASSED
- {re-run /topic-implement} — if FAILED
```
