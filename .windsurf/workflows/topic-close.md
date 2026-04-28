---
description: Close a ticket — final review, multi-commit (code first, docs last), report, and session log
---

# TOPIC CLOSE — Ticket Closure

## 🌐 Project Override Hook

This is a **global workflow**. Before executing the steps below, check whether the host project provides an override for it.

**Where to look** — in the host project's workspace root (NOT in the global workflows repo):

```
.windsurf/overrides/workflows/<same-filename-as-this-file>.md
```

Use the same filename as this workflow file's basename.

**If that override file exists**, read it and apply the first matching mode (declared in frontmatter as `mode: replace|extend|modify`, OR via a first-heading marker `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — IGNORE the steps below; execute ONLY the override file's instructions. The global workflow is fully shadowed.
- **EXTEND** — execute the steps below as written, then execute the override as an additional phase (or at the position the override specifies, e.g. "after Step 3").
- **MODIFY** — execute the steps below, but substitute / insert the steps the override declares (typically by step number or step title).

**If no override file exists**, ignore this section and proceed with the steps below as written.

If the override file's mode is missing or ambiguous, **STOP and ask the user** which mode applies before proceeding.

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

Final workflow for closing a ticket. Performs final code review, creates
**multiple meaningful commits for code changes first**, writes all
documentation artifacts (Completion Report, CHANGELOG, README updates), runs
`/log-session` to reconcile session logs, and creates **one final docs
commit** at the end.

Use @code-reviewer mindset for the final review step.

## Inputs

- **TICKET_ID**: e.g. `T-04`
- **TOPIC_SLUG** (optional): defaults to auto-detect from branch / ticket ID

## Commit-Ordering Contract

This workflow enforces:

1. **Code commits come FIRST** — one or more, grouped into meaningful units.
2. **Docs commit comes LAST** — a single commit that captures everything in
   `docs/` (reports, CHANGELOG, README, SESSION-CHANGES, WORK_LOG) + any
   `.windsurf/` workflow/template/skill updates made during this ticket.

Never push. User controls when to push.

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**

Read and internalize the skill file:
- Read `.windsurf/skills/code-reviewer/SKILL.md`

### 1. Gather Context

// turbo

Read:
- `docs/{TOPIC_SLUG}/tickets/{TICKET_ID}__*.md` — ticket spec with AC
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — current state
- `docs/{TOPIC_SLUG}/README.md` — current status table
- **Verification Report** from `/topic-verify`:
  `docs/{TOPIC_SLUG}/reports/{TICKET_ID}__*__VERIFICATION_REPORT.md` (required —
  this is the primary source of truth for closure)
- Implementation report from conversation (from `/topic-implement`, if still
  in context)
- `docs/HOW_TO_USE.md` and `docs/LINKING-RULES.md` (convention reference)

If the verification report does not exist, **stop and recommend running
`/topic-verify {TICKET_ID}` first.**

### 2. Final Review

Quick code-reviewer-mindset review of all changes for this ticket:

- Run `git status` / `git diff --stat HEAD` — list all uncommitted changes
- Verify no accidental files were modified outside scope
- Confirm all changes align with ticket spec and guardrails
- Confirm the verification report's quality gate is all ✅
- Confirm any inline markers (e.g., `BOOTSTRAP5-COMPAT`) map 1:1 to inventory

If issues found, fix minor ones immediately. For scope questions, ask user.

### 3. Plan the Commit Groups

Identify **logical commit groups** for the code changes. Good grouping
heuristics:

- **Build / config changes** — `angular.json`, `package.json`, `tsconfig.*`,
  global style shims in `src/styles.scss`
- **Mass mechanical changes** — utility renames across many files (one
  commit)
- **Localized behavioral changes** — per-feature refactors (one commit per
  feature area if volumes warrant it)
- **Scoped shims / compat code** — one commit per shim (with inline marker +
  inventory entry together)
- **Tests** — if tests are touched, usually accompany the code they verify;
  occasionally justify a separate commit if they stand alone
- **Docs** — ALWAYS a separate final commit. Do not mix with code.

For each group, draft:

- **Subject line:** `{TICKET_ID}: {short imperative description}` (≤72 chars)
- **Body:** bullet list of what the commit does and why (wrap at 72 chars)
- **Files included:** exact list

Present the plan to the user BEFORE committing.

**STOP** and wait for user approval of the commit group plan. Adjust if
requested.

### 4. Create Code Commits (one by one)

For each code commit group in the approved plan:

1. Stage only the files belonging to that group: `git add {files}`
2. Create the commit: `git commit -m "{subject}" -m "{body}"`
3. Record the resulting short hash for the session log.
4. Run `git status` to confirm the staging area is clean for the next group.

Do NOT run any push commands.

Do NOT include any files under `docs/` or `.windsurf/` in code commits —
those belong to the final docs commit.

After all code commits land, run `git log --oneline -N` (where N = number of
code commits just made) to verify the expected commits are present in order.

### 5. Write Completion Report

Create `docs/{TOPIC_SLUG}/reports/{TICKET_ID}__{short_slug}__REPORT.md` using
`.windsurf/_templates/ticket-report-template.md`.

This is a standalone narrative of the ticket — what was done, grouped
logically, with AC table, build/test results, files modified, and net impact.

Consume the Verification Report from §1 as the primary data source.

### 6. Update Topic CHANGELOG.md

Append entry to `docs/{TOPIC_SLUG}/CHANGELOG.md` under today's date using the
topic's existing changelog conventions. Include:

- Ticket ID and title
- Bullet list of changes
- Links to ticket spec, completion report, and verification report
- Git commit hashes (from §4)

If today's date section already exists, append under it — do not duplicate
the date heading.

### 7. Update Topic README.md

In `docs/{TOPIC_SLUG}/README.md`:
- Mark the ticket as ✅ Complete in the status table
- Add links to completion report and verification report
- Update the phase status if this ticket completes a phase

### 8. Write / Append Daily Report (if relevant)

If the topic uses daily reports (e.g., `docs/{TOPIC_SLUG}/report_YYYY-MM-DD.md`):

- If no report exists for today, create one using
  `.windsurf/_templates/daily-report-template.md` (non-technical stakeholder
  summary).
- If one exists, append this ticket's summary to it.

Skip this step if the topic does not use daily reports.

### 9. Run /log-session

Execute `/log-session` fully. This reconciles:

- Today's `SESSION-CHANGES-{DATE}.md` in the topic
- Topic `CHANGELOG.md` session entry (idempotent if already touched in §6)
- Root `docs/WORK_LOG.md` session entry
- Link hygiene

Pass `TICKET_ID`, `TOPIC_SLUG`, `DATE`, and `SESSION_TYPE=closure` if the
child workflow accepts them.

### 10. Create Final Docs Commit (LAST COMMIT)

Stage every remaining uncommitted change. These should all be docs or
workflow/template/skill updates.

```bash
git status        # confirm only docs/ and/or .windsurf/ changes remain
git add -A docs   # stage all docs changes
git add -A .windsurf  # stage any workflow/template/skill changes
git status        # final check
```

Compose the final commit:

- **Subject:** `{TICKET_ID}: close ticket — docs, reports, and session log`
- **Body:** bullet list of what was added/updated:
  - Completion Report path
  - Verification Report path (created earlier by `/topic-verify`)
  - Topic CHANGELOG / README updates
  - SESSION-CHANGES file for today
  - Root WORK_LOG entry
  - Any workflow / template / skill additions (only if this ticket produced
    any — typically it won't)

```bash
git commit -m "{subject}" -m "{body}"
```

Record the resulting hash.

### 11. Post-Commit Reconciliation

After the final docs commit, update the SESSION-CHANGES and WORK_LOG entries
to include the **final docs commit hash** (§10). This is a small edit to
fill in the previously unknown hash.

Stage + commit this fix ONLY if the previously-recorded hash was a clearly
wrong placeholder. If the hashes were left empty in §9 and filled here, this
is a normal idempotent update — create a tiny amend or a second docs-only
commit labeled `{TICKET_ID}: record final commit hashes`.

(Preferred: fill hashes after §4 and before §10 if the workflow supports
two-pass editing. If not, accept a minor follow-up amend here.)

### 12. Final Report

```
✅ {TICKET_ID} Closed

📄 Reports:
  Completion:   docs/{TOPIC_SLUG}/reports/{TICKET_ID}__*__REPORT.md
  Verification: docs/{TOPIC_SLUG}/reports/{TICKET_ID}__*__VERIFICATION_REPORT.md

📝 Docs updated:
  Topic CHANGELOG: ✅
  Topic README:    ✅ ticket marked complete
  Daily report:    {created | updated | n/a}
  SESSION-CHANGES: ✅
  Root WORK_LOG:   ✅

🔀 Commits created (in order, NOT pushed):
  {hash1}  {TICKET_ID}: {subject 1}
  {hash2}  {TICKET_ID}: {subject 2}
  {hash3}  {TICKET_ID}: {subject 3}
  {hash4}  {TICKET_ID}: close ticket — docs, reports, and session log   (LAST = docs)

🎯 Next:
- Review the commit sequence: git log --oneline -N
- Push when ready: git push
- Next ticket: {NEXT_TICKET_ID} — {title}
  → /kickoff {NEXT_TICKET_ID}
```

---

## Notes

- This is the ONLY workflow that **creates commits**. Implementation
  (`/topic-implement`) and verification (`/topic-verify`) never commit.
- **Never push** — user controls when to push.
- **Docs commit is always LAST** — strictly enforced.
- **Code commits are multiple** — group meaningfully. Don't over-split (a
  2-line change doesn't deserve its own commit) or under-split (a 500-file
  mixed change isn't one commit).
- If verification wasn't run, stop and recommend `/topic-verify` first.
- The Completion Report should be standalone — readable by someone who didn't
  follow the conversation.
- Always verify `ng build` and `ng test` pass before creating commits (they
  should have already passed via `/topic-verify`).
