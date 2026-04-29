---
name: topic-closure
description: Close a ticket — final review, multi-commit (code first, docs last), Completion Report, CHANGELOG / README updates, session-logging reconciliation. The ONLY skill that creates commits.
---

# Topic Closure Skill

## Persona

**MANDATORY first action:** Load the Reviewer persona by reading `.windsurf/personas/reviewer.md`. Adopt it for the duration of this skill (especially Step 2 final review).

## Inputs

- **TICKET_ID** (required) — e.g. `T-04`.
- **TOPIC_SLUG** (optional) — auto-detect from branch / ticket ID.

## Output

- Code commits (one or more, grouped meaningfully).
- One final docs commit (LAST commit; captures everything in `docs/` + `.windsurf/`).
- `docs/{TOPIC_SLUG}/reports/{TICKET_ID}__{short_slug}__REPORT.md` (Completion Report) using `.windsurf/_templates/ticket-report-template.md`.
- Updated `docs/{TOPIC_SLUG}/CHANGELOG.md` and `docs/{TOPIC_SLUG}/README.md`.
- Reconciled SESSION-CHANGES + WORK_LOG via `session-logging` skill.
- (Optional) updated daily report.
- **Never push.** User controls when to push.

## Commit-Ordering Contract

1. **Code commits come FIRST** — one or more, grouped into meaningful units.
2. **Docs commit comes LAST** — single commit capturing everything in `docs/` (reports, CHANGELOG, README, SESSION-CHANGES, WORK_LOG) + any `.windsurf/` updates made during this ticket.

## Procedure

### 1. Gather context

Read:
- `docs/{TOPIC_SLUG}/tickets/{TICKET_ID}__*.md` — ticket spec with AC.
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — current state.
- `docs/{TOPIC_SLUG}/README.md` — current status table.
- **Verification Report** from `/topic-verify`: `docs/{TOPIC_SLUG}/reports/{TICKET_ID}__*__VERIFICATION_REPORT.md` (REQUIRED).
- Implementation report from conversation if still in context.

If the verification report does not exist, **STOP** and recommend running `/topic-verify {TICKET_ID}` first.

### 2. Final review

Reviewer-persona pass over all changes for this ticket:
- Run `git status` / `git diff --stat HEAD` — list all uncommitted changes.
- Verify no accidental files outside scope.
- Confirm all changes align with ticket spec and guardrails.
- Confirm verification report's quality gate is all ✅.
- Confirm any inline shim markers (if any) map 1:1 to their inventory file.

If issues found, fix minor ones immediately. For scope questions, ask user.

### 3. Plan the commit groups

Identify **logical commit groups** for code changes. Heuristics:

- **Build / config changes** — build-tool config, dependency manifest / lockfile, type-config, global style or runtime initialization.
- **Mass mechanical changes** — utility renames across many files (one commit).
- **Localized behavioral changes** — per-feature refactors (one commit per feature area if volumes warrant).
- **Scoped shims / compat code** — one commit per shim (with inline marker + inventory entry together).
- **Tests** — usually accompany the code they verify; occasionally separate if standalone.
- **Docs** — ALWAYS a separate final commit. Do not mix with code.

For each group, draft:
- **Subject:** `{TICKET_ID}: {short imperative description}` (≤72 chars)
- **Body:** bullet list of what + why (wrap at 72 chars)
- **Files included:** exact list

Present plan to user BEFORE committing. **STOP** and wait for approval. Adjust if requested.

### 4. Create code commits (one by one)

For each approved code commit group:

1. Stage only the files belonging to that group: `git add {files}`.
2. Create the commit: `git commit -m "{subject}" -m "{body}"`.
3. Record the resulting short hash for the session log.
4. Run `git status` to confirm staging area is clean for the next group.

Do NOT push.

Do NOT include `docs/` or `.windsurf/` files in code commits — they belong to the final docs commit.

After all code commits land, run `git log --oneline -N` (N = number of code commits) to verify expected commits are present in order.

### 5. Write Completion Report

Create `docs/{TOPIC_SLUG}/reports/{TICKET_ID}__{short_slug}__REPORT.md` using `.windsurf/_templates/ticket-report-template.md`. Standalone narrative — what was done, grouped logically, AC table, build/test results, files modified, net impact. Consume Verification Report from §1 as primary data source.

### 6. Update Topic CHANGELOG

Append entry to `docs/{TOPIC_SLUG}/CHANGELOG.md` under today's date using existing changelog conventions:

- Ticket ID and title
- Bullet list of changes
- Links to ticket spec, completion report, verification report
- Git commit hashes (from §4)

If today's date section exists, append under it — do not duplicate the date heading.

### 7. Update Topic README

In `docs/{TOPIC_SLUG}/README.md`:
- Mark ticket as ✅ Complete in status table.
- Add links to completion + verification reports.
- Update phase status if this ticket completes a phase.

### 8. Daily report (if relevant)

If the topic uses daily reports (e.g., `docs/{TOPIC_SLUG}/report_YYYY-MM-DD.md`):

- If no report exists for today, create using `.windsurf/_templates/daily-report-template.md`.
- If one exists, append this ticket's summary.

Skip if topic doesn't use daily reports.

### 9. Run session-logging

Execute `session-logging` skill fully. Reconciles:
- Today's `SESSION-CHANGES-{DATE}.md`
- Topic `CHANGELOG.md` session entry (idempotent if already touched in §6)
- Root `docs/WORK_LOG.md` session entry
- Link hygiene

### 10. Create final docs commit (LAST commit)

Stage all remaining uncommitted changes (should all be docs / `.windsurf/`).

```bash
git status         # confirm only docs/ and/or .windsurf/ changes remain
git add -A docs
git add -A .windsurf
git status         # final check
```

Compose final commit:
- **Subject:** `{TICKET_ID}: close ticket — docs, reports, and session log`
- **Body:** bullet list of what was added/updated.

```bash
git commit -m "{subject}" -m "{body}"
```

Record the resulting hash.

### 11. Post-commit reconciliation

After final docs commit, update SESSION-CHANGES and WORK_LOG entries to include the final docs commit hash. Small idempotent edit.

## Final Report

```
✅ {TICKET_ID} Closed

📄 Reports:
  Completion:   docs/{TOPIC_SLUG}/reports/{TICKET_ID}__*__REPORT.md
  Verification: docs/{TOPIC_SLUG}/reports/{TICKET_ID}__*__VERIFICATION_REPORT.md

📝 Docs updated:
  Topic CHANGELOG: ✅
  Topic README: ✅ ticket marked complete
  Daily report: {created | updated | n/a}
  SESSION-CHANGES: ✅
  Root WORK_LOG: ✅

🔀 Commits created (in order, NOT pushed):
  {hash1}  {TICKET_ID}: {subject 1}
  {hash2}  {TICKET_ID}: {subject 2}
  {hash3}  {TICKET_ID}: close ticket — docs, reports, and session log   (LAST = docs)

🎯 Next:
- Review: git log --oneline -N
- Push when ready: git push
- Next ticket: {NEXT_TICKET_ID} — {title} → /kickoff {NEXT_TICKET_ID}
```

Then **STOP**.

## Rules

- This is the ONLY skill that creates commits.
- **Never push** — user controls when to push.
- **Docs commit is always LAST** — strictly enforced.
- **Code commits are multiple** — group meaningfully. Don't over-split or under-split.
- If verification wasn't run, STOP and recommend `/topic-verify` first.
- Completion Report must be standalone — readable by someone who didn't follow the conversation.
