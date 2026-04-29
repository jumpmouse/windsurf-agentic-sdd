---
description: Close a ticket — final review, multi-commit (code first, docs last), Completion Report, CHANGELOG / README updates, session-logging reconciliation.
---

# /topic-close — Wrap It Up

Thin entry point. Loads the `topic-closure` skill (which loads `@Personas:reviewer`) and executes its full procedure. **This is the only workflow that creates commits.**

## Inputs

- **TICKET_ID** (required) — e.g. `T-04`.
- **TOPIC_SLUG** (optional) — auto-detect from current branch / ticket ID.

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/topic-closure/SKILL.md` end-to-end with the inputs above. The skill:

1. Loads `@Personas:reviewer`.
2. Reads ticket spec + Verification Report (REQUIRED — STOPs if missing).
3. Final review (git status, scope check, quality gate confirmation).
4. Plans commit groups (code commits first, docs commit LAST). **STOPs at user-review gate** for commit-plan approval.
5. Creates code commits one by one (does NOT push).
6. Writes Completion Report (`{TICKET_ID}__*__REPORT.md`).
7. Updates topic CHANGELOG + README + (optional) daily report.
8. Calls `session-logging` to reconcile SESSION-CHANGES + WORK_LOG.
9. Creates the FINAL docs commit (everything in `docs/` + `.windsurf/`).
10. Post-commit reconciliation (fills in final docs commit hash where it was unknown).

### 2. STOP

The skill handles its own STOP / report. Never pushes.

## After it finishes

- Review commit sequence: `git log --oneline -N`.
- Push when ready: `git push`.
- Next ticket: `/kickoff {NEXT_TICKET_ID}`.
