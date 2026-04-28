# Work Log Entry Template

> **Reference:** `docs/WORK_LOG.md` is the **single session-based log** at the
> repo root. There is no separate root CHANGELOG — the WORK_LOG captures both
> "what was done" and "when it was done" with full references to topic, ticket,
> commits, and reports.
>
> Entries go **newest first** at the top of `docs/WORK_LOG.md`, under the
> appropriate topic section (or in a new topic section if it's a new topic).

---

## Topic Section Template

When creating a new topic, add a new top-level section to `WORK_LOG.md`:

```markdown
# ({YYYY-MM-DD} — ongoing) {Topic Title} ({topic-slug}) — {🔄 In Progress | ✅ Complete | ⏸ On Hold}

[Detailed plan](./{topic-slug}/README.md)

{1–3 sentence summary of the topic's purpose.}

## Phases

- [x] Phase 0 — {Phase name} ({completion date if done})
- [x] Phase 1 — {Phase name} ({completion date})
- [ ] Phase 2 — {Phase name}
- [ ] Phase 3 — {Phase name}

## Sessions

{Session entries go here, newest first. See "Session Entry Template" below.}

---
```

When the topic closes, update the section title to:

```markdown
# ({YYYY-MM-DD} to {YYYY-MM-DD}) {Topic Title} ({topic-slug}) — ✅ Complete
```

---

## Session Entry Template

Each work session produces ONE entry under the relevant topic's "Sessions"
subsection. Newest first.

```markdown
### {YYYY-MM-DD} — {session type: implementation | verification | closure | research}: {TICKET_ID}: {ticket title}

**What was done:**

- {bullet of the main outcome}
- {bullet}
- {bullet}

**Ticket:** [{TICKET_ID}](./{topic-slug}/tickets/{TICKET_FILENAME}.md)
**Reports:**
- [Completion Report](./{topic-slug}/reports/{TICKET_ID}__{FILENAME}__REPORT.md)
- [Verification Report](./{topic-slug}/reports/{TICKET_ID}__{FILENAME}__VERIFICATION_REPORT.md)

**Session changes:** [SESSION-CHANGES-{YYYY-MM-DD}.md](./{topic-slug}/SESSION-CHANGES-{YYYY-MM-DD}.md)

**Commits:**

| # | Hash | Message |
|---|------|---------|
| 1 | `{short_hash}` | {message subject} |
| 2 | `{short_hash}` | {message subject} |

**Build/Test status at session end:**

- Build: ✅ / ❌
- Tests: {N} passed, {M} skipped

{Optional one-liner: decisions, follow-ups, or flags for next session.}

---
```

---

## Full Example (for reference)

```markdown
# (2026-04-16 — ongoing) Angular 15 → 21 Upgrade (app-update-v21) — 🔄 In Progress

[Detailed plan](./app-update-v21/README.md)

Incremental migration of the SXE User Frontend from Angular 15 to Angular 21,
including all third-party library upgrades, Bootstrap 4→5 CSS migration, jQuery
removal, and test infrastructure modernization.

## Phases

- [x] Phase 0 — Pre-Migration Cleanup (T-01, T-02)
- [ ] Phase 1 — Angular 16 + View Engine Unblock (T-03, T-03a, T-03b, T-04 ✅, T-05, T-06, T-07)
- [ ] Phase 2 — Angular 17 + SSR (T-08..T-11)
- [ ] Phase 3 — Angular 18 + HttpClient (T-12..T-14)
- [ ] Phase 4 — Angular 19 + Standalone (T-15..T-16)
- [ ] Phase 5 — Angular 20 + Build Migration (T-17, T-18)
- [ ] Phase 6 — Angular 21 + Final (T-19..T-22)

## Sessions

### 2026-04-28 — closure: T-04: Bootstrap 4→5 CSS + ng-bootstrap API migration

**What was done:**

- Swept repo for all Bootstrap 4 utility classes; migrated 105 HTML + 2 SCSS
  files to BS5 equivalents
- Added global `.form-group` and `.input-group-append` compatibility shims with
  `BOOTSTRAP5-COMPAT` markers; inventory in `bootstrap-shim.md`
- Preserved popup custom-SVG close button via SCSS shim
- No `data-*` attribute renames (T-06 scope)

**Ticket:** [T-04](./app-update-v21/tickets/T-04__Bootstrap_4→5_CSS_+_ng-bootstrap_API_migration.md)
**Reports:**
- [Completion Report](./app-update-v21/reports/T-04__Bootstrap_4→5_CSS_+_ng-bootstrap_API_migration__REPORT.md)
- [Verification Report](./app-update-v21/reports/T-04__Bootstrap_4→5_CSS_+_ng-bootstrap_API_migration__VERIFICATION_REPORT.md)

**Session changes:** [SESSION-CHANGES-2026-04-28.md](./app-update-v21/SESSION-CHANGES-2026-04-28.md)

**Commits:**

| # | Hash | Message |
|---|------|---------|
| 1 | `{hash1}` | T-04: switch styles to Bootstrap 5 + add global compatibility shims |
| 2 | `{hash2}` | T-04: migrate Bootstrap 4 utility classes to Bootstrap 5 |
| 3 | `{hash3}` | T-04: preserve popup custom-SVG close button via BS4 compat shim |
| 4 | `{hash4}` | T-04: close ticket + standardize doc system + new workflows |

**Build/Test status at session end:**

- Build: ✅ (exit 0, hash e918b1f3f4c38283)
- Tests: 331 passed, 129 skipped (matches baseline)

Next: T-05 (Custom ledger block navigator — replace slick-carousel).
```
