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

A worked example with a real project's content is preserved at:

- `.windsurf/_templates/examples/work-log-entry__sxe-bootstrap-migration.md`

That file shows what every placeholder above resolves to in a real project (Angular 15→21 migration / Bootstrap 4→5 CSS migration). Read it as a sanity check, but do **NOT** copy its project-specific details into your own `WORK_LOG.md`.
