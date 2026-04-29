---
name: session-logging
description: Reconcile session documentation — create / update SESSION-CHANGES file, append topic CHANGELOG entry, append root WORK_LOG entry. Idempotent. Used at end of working session and called by spec-chain skills.
---

# Session Logging Skill

## Persona

**MANDATORY first action:** Load the Reviewer persona by reading `.windsurf/personas/reviewer.md`. The Reviewer mindset is right for this skill because it's about **idempotency, link hygiene, and no-duplicates discipline** — quality-control concerns.

## Inputs (all optional)

- **TOPIC_SLUG** — topic folder slug. Default: auto-detect most recently modified topic under `docs/` with both `README.md` and `CHANGELOG.md`.
- **DATE** — `YYYY-MM-DD`. Default: today (system date).
- **SESSION_TYPE** — one of `implementation | verification | closure | research | hot-fix`. Default: infer from recent git activity.

## Output

- Created / updated `docs/{TOPIC_SLUG}/SESSION-CHANGES-{DATE}.md` using `.windsurf/_templates/session-changes-template.md`.
- Appended entry in `docs/{TOPIC_SLUG}/CHANGELOG.md` under today's date.
- Appended / updated session entry in `docs/WORK_LOG.md` under the topic's section.

**Does NOT create commits.** The calling skill or user decides when to commit.

## Procedure

### 1. Identify target topic

- If `TOPIC_SLUG` provided, use `docs/{TOPIC_SLUG}/`.
- Otherwise, scan `docs/` for subfolders containing both `README.md` and `CHANGELOG.md`, sorted by most-recent file modification. Pick newest.
- If multiple topics had recent changes (git diff shows changes across multiple `docs/{topic}/` folders), **STOP and ask** the user to specify, or run once per topic.

If no candidate topic found, report error and ask for `TOPIC_SLUG`.

### 2. Gather session context

Read:
- `docs/{TOPIC_SLUG}/README.md` — topic status.
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — last date section (avoid duplicates).
- The newest `docs/{TOPIC_SLUG}/SESSION-CHANGES-*.md` (if any) — last session's state.
- `docs/WORK_LOG.md` — topic section + latest session entry.
- `git status` + `git diff --name-only HEAD` — files changed since `HEAD`.
- `git log --oneline -20` — recent commits.

Identify:
- Which **ticket(s)** were worked on — infer from branch name, recent commits, topic's open tickets.
- What **session type** applies — if ambiguous, ask the user.

### 3. Create / update SESSION-CHANGES file

Create or update `docs/{TOPIC_SLUG}/SESSION-CHANGES-{DATE}.md` using `.windsurf/_templates/session-changes-template.md`. Required sections:

- **Context** — topic, ticket(s), branch, session type.
- **Files Changed** — grouped by repo / area; every path prefixed with `file:`.
- **What Was Done** — concrete outcomes.
- **Commits Created** — table with hash + subject (placeholders if commits haven't happened yet, fill later).
- **Next Steps** — next actionable item with workflow reference.

**Idempotency rule:** if the file already exists for today, APPEND new files / new "What Was Done" bullets. Don't rewrite from scratch.

### 4. Update Topic CHANGELOG

Append (or merge into) a `## {DATE}` section in `docs/{TOPIC_SLUG}/CHANGELOG.md` using existing changelog conventions:

```markdown
## {DATE}

### {TICKET_ID}: {ticket title}
- {bullet}
- **Ticket:** [{TICKET_ID}](tickets/{TICKET_FILENAME}.md)
- **Report:** [{TICKET_ID} Report](reports/{REPORT_FILENAME}.md)
- **Verification:** [{TICKET_ID} Verification](reports/{VERIFICATION_REPORT_FILENAME}.md)
- **Session changes:** [SESSION-CHANGES-{DATE}.md](SESSION-CHANGES-{DATE}.md)
- **Commits:** `{hash1}`, `{hash2}`, ...
```

**Idempotency rule:** if the date section already exists, APPEND bullets under it. No duplicate date heading.

### 5. Update Root WORK_LOG

In `docs/WORK_LOG.md`:

1. Find the topic's section. If absent, add a new one at the top using `.windsurf/_templates/work-log-entry-template.md`.

2. Under topic's `## Sessions` subsection, add (or merge) a session entry for today:

   ```markdown
   ### {DATE} — {session type}: {TICKET_ID}: {ticket title}

   **What was done:**
   - {bullet}

   **Ticket:** [{TICKET_ID}](./{TOPIC_SLUG}/tickets/{TICKET_FILENAME}.md)
   **Reports:**
   - [Completion Report](./{TOPIC_SLUG}/reports/{REPORT_FILENAME}.md)
   - [Verification Report](./{TOPIC_SLUG}/reports/{VERIFICATION_REPORT_FILENAME}.md)

   **Session changes:** [SESSION-CHANGES-{DATE}.md](./{TOPIC_SLUG}/SESSION-CHANGES-{DATE}.md)

   **Commits:**
   | # | Hash | Message |
   |---|------|---------|
   | 1 | `{hash1}` | {subject} |
   ```

3. Update topic's phase checkboxes if any phase completed this session.

4. If the topic itself closed this session, update topic header from `(YYYY-MM-DD — ongoing) ... — 🔄 In Progress` to `(YYYY-MM-DD to YYYY-MM-DD) ... — ✅ Complete`.

**Idempotency rule:** if today's session entry exists, APPEND new bullets and commit rows.

### 6. Link hygiene

Verify:
- [ ] Each `docs/{TOPIC_SLUG}/*.md` (except `SESSION-CHANGES-*.md`) starts with the project's standard backlink.
- [ ] `docs/{TOPIC_SLUG}/README.md` links to its CHANGELOG and the latest SESSION-CHANGES.
- [ ] Every `file:path` in the new SESSION-CHANGES entry resolves.

Fix any broken link.

### 7. Verification

- [ ] `SESSION-CHANGES-{DATE}.md` exists and is non-empty.
- [ ] Topic `CHANGELOG.md` has a `## {DATE}` section.
- [ ] Root `WORK_LOG.md` has a session entry for today under the topic's Sessions subsection.
- [ ] Topic's phase checkboxes reflect current progress.
- [ ] No broken internal links.
- [ ] No duplicate date sections / session entries.

## Final Report

```
✅ Session Logged

📁 Topic: docs/{TOPIC_SLUG}/
📅 Date: {DATE}
🎫 Ticket: {TICKET_ID or "—"}
📝 Session type: {session_type}

📝 Documents updated:
- SESSION-CHANGES-{DATE}.md ({created | appended})
- docs/{TOPIC_SLUG}/CHANGELOG.md ({appended to {DATE} section})
- docs/WORK_LOG.md ({topic section | topic + session entry})

🎯 Next:
- If called from /topic-close, continue parent skill.
- If standalone, consider committing updated docs.
```

## Error Handling

| Error | Action |
|-------|--------|
| No target topic detected | Ask user for `TOPIC_SLUG`. |
| Multiple topics with recent changes | STOP. Ask user to specify topic or run once per topic. |
| `WORK_LOG.md` missing | Create using `.windsurf/_templates/work-log-entry-template.md` pattern. |
| Topic `CHANGELOG.md` missing | Create with `# Changelog — {topic title}` header + standard backlink. |
| Template file missing | Report and halt; do not guess template contents. |

## Notes

- Designed to be **called from `topic-closure` Step 9** and also **callable standalone** mid-ticket.
- Does NOT create commits.
- Does NOT update per-ticket completion / verification reports — those are written by `topic-verification` and `topic-closure` themselves.
