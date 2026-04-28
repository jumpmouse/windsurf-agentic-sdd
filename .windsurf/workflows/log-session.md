---
description: Reconcile session documentation — SESSION-CHANGES, topic CHANGELOG, and root WORK_LOG
---

# LOG SESSION — Session Reconciliation

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

Standalone workflow. Reconciles documentation after a work session — creates /
updates the day's `SESSION-CHANGES-YYYY-MM-DD.md`, appends an entry to the
topic `CHANGELOG.md`, and appends / updates the session entry in the root
`WORK_LOG.md`.

**Idempotent:** running this workflow twice on the same day does NOT create
duplicate entries; it appends new information only.

**Does NOT create commits.** The calling workflow (or the user) decides when
to commit.

## Inputs (optional)

- **TOPIC_SLUG**: Topic folder slug. Default: auto-detect the most recently
  modified topic under `docs/` that has a `README.md` and a `CHANGELOG.md`.
- **DATE**: Date string in `YYYY-MM-DD`. Default: today (system date).
- **SESSION_TYPE**: One of `implementation | verification | closure | research
  | hot-fix`. Default: inferred from recent git activity.

## Steps

### 1. Identify Target Topic

// turbo

- If `TOPIC_SLUG` was provided, use `docs/{TOPIC_SLUG}/`.
- Otherwise, detect the target topic by scanning `docs/` for subfolders
  containing both `README.md` and `CHANGELOG.md`, sorted by most-recent file
  modification time. Pick the newest one.
- If multiple topics were clearly worked on (git diff shows changes across
  multiple `docs/{topic}/` folders), **stop and ask the user** to specify
  which topic this session belongs to, or run `/log-session` once per topic.

If no candidate topic is found, report the error and ask for a `TOPIC_SLUG`.

### 2. Gather Session Context

// turbo

Read these files to build the session-change picture:

- `docs/{TOPIC_SLUG}/README.md` — topic status
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — last date section (to avoid duplicates)
- The newest `docs/{TOPIC_SLUG}/SESSION-CHANGES-*.md` (if any) — last session's
  state
- `docs/WORK_LOG.md` — topic section and latest session entry
- `git status` + `git diff --name-only HEAD` — files changed in the working
  tree since `HEAD`
- `git log --oneline -20` — recent commits (to capture any that landed this
  session)

Identify:

- Which **ticket(s)** (if any) were worked on — infer from branch name, recent
  commit messages, or topic's open tickets.
- What **session type** applies — if ambiguous, ask the user.

### 3. Create / Update SESSION-CHANGES File

Create or update `docs/{TOPIC_SLUG}/SESSION-CHANGES-{DATE}.md` using
`.windsurf/_templates/session-changes-template.md`.

Required sections:

- **Context:** topic, ticket(s), branch, session type
- **Files Changed:** grouped by repo / area; every file path prefixed with `file:`
- **What Was Done:** bullet list of concrete outcomes
- **Commits Created:** table with hash + message subject — fill in placeholders
  if commits haven't happened yet, then update after commit step
- **Next Steps:** the next actionable item with a workflow reference

**Idempotency rule:** if the file already exists for today, APPEND new files /
new "What Was Done" bullets, don't re-write from scratch.

### 4. Update Topic CHANGELOG

Append (or merge into) a `## {DATE}` section in `docs/{TOPIC_SLUG}/CHANGELOG.md`
using the existing changelog conventions for this topic.

Typical entry shape:

```markdown
## {DATE}

### {TICKET_ID}: {ticket title}
- {bullet summarizing the outcome}
- {bullet}
- **Ticket:** [{TICKET_ID}](tickets/{TICKET_FILENAME}.md)
- **Report:** [{TICKET_ID} Report](reports/{REPORT_FILENAME}.md)
- **Verification:** [{TICKET_ID} Verification](reports/{VERIFICATION_REPORT_FILENAME}.md)
- **Session changes:** [SESSION-CHANGES-{DATE}.md](SESSION-CHANGES-{DATE}.md)
- **Commits:** `{hash1}`, `{hash2}`, ...
```

**Idempotency rule:** if the date section already exists, APPEND new bullets
under it. Do not create a duplicate date heading.

### 5. Update Root WORK_LOG

In `docs/WORK_LOG.md`:

1. Find the topic's section. If the topic doesn't yet have a section, add a
   new one at the top of the file using
   `.windsurf/_templates/work-log-entry-template.md`.

2. Under the topic's "Sessions" subsection, add (or merge) a session entry
   for today:

   ```markdown
   ### {DATE} — {session type}: {TICKET_ID}: {ticket title}

   **What was done:**
   - {bullet}
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

3. Update the topic's phase checkboxes if any phase completed this session.

4. If the topic itself closed this session, update the topic header from
   `(YYYY-MM-DD — ongoing) ... — 🔄 In Progress` to
   `(YYYY-MM-DD to YYYY-MM-DD) ... — ✅ Complete`.

**Idempotency rule:** if a session entry for today already exists, APPEND
new "What was done" bullets and new commit rows; don't duplicate.

### 6. Link Hygiene

Verify these links exist and resolve (using `ls` or file-exists check):

- [ ] Each `docs/{TOPIC_SLUG}/*.md` file (except `SESSION-CHANGES-*.md`) starts
      with `[← Back to README](./README.md)` per `LINKING-RULES.md`
- [ ] `docs/{TOPIC_SLUG}/README.md` links to its CHANGELOG and the latest
      SESSION-CHANGES
- [ ] Every `file:path` reference in the new SESSION-CHANGES entry points to
      an actual file

Fix any broken link found.

### 7. Verification

Before reporting, confirm:

- [ ] `SESSION-CHANGES-{DATE}.md` exists and is non-empty
- [ ] Topic `CHANGELOG.md` has a `## {DATE}` section
- [ ] Root `WORK_LOG.md` has a session entry for today under the topic's
      "Sessions" subsection
- [ ] Topic's phase checkboxes in `WORK_LOG.md` reflect current progress
- [ ] No broken internal links
- [ ] No duplicate date sections / session entries

### 8. Report

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

🔢 Session entry contents:
- What was done: {N} bullets
- Files changed: {N}
- Commits: {N} recorded

🎯 Next:
- If called from /topic-close, continue parent workflow
- If standalone, consider committing updated docs
```

---

## Error Handling

| Error | Action |
|-------|--------|
| No target topic detected | Ask user to supply `TOPIC_SLUG` |
| Multiple topics have recent changes | Stop. Ask user to specify topic or to run once per topic |
| `WORK_LOG.md` missing | Create using `.windsurf/_templates/work-log-entry-template.md` pattern |
| Topic `CHANGELOG.md` missing | Create with `# Changelog — {topic title}` header + `[← Back to README](./README.md)` backlink |
| Template file missing | Report and halt; do not guess template contents |

## Notes

- Designed to be **called from `/topic-close` Step 8** and also **callable
  standalone** mid-ticket (e.g., at end of day before committing, or after a
  research / investigation session that produced no code).
- Does NOT create commits. The parent workflow (`/topic-close`) handles commit
  creation separately.
- Does NOT update per-ticket completion or verification reports — those are
  created by `/topic-verify` and `/topic-close` themselves.
- **Follows HOW_TO_USE.md conventions** — see `docs/HOW_TO_USE.md` for the
  authoritative rules on where each doc type lives.
