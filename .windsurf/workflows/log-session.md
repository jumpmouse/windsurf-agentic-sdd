---
description: Reconcile session documentation — SESSION-CHANGES, topic CHANGELOG, and root WORK_LOG. Idempotent.
---

# /log-session — Reconcile Session Docs

Thin entry point. Loads the `session-logging` skill (which loads `@Personas:reviewer`) and executes its full procedure.

## Inputs (all optional)

- **TOPIC_SLUG** — auto-detect most recently modified topic if not provided.
- **DATE** — `YYYY-MM-DD`. Default: today.
- **SESSION_TYPE** — `implementation | verification | closure | research | hot-fix`. Default: infer from git.

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/session-logging/SKILL.md` end-to-end with the inputs above. The skill:

1. Loads `@Personas:reviewer` (for link-hygiene + idempotency discipline).
2. Identifies target topic.
3. Gathers session context (git status / diff / log + recent docs).
4. Creates / updates `SESSION-CHANGES-{DATE}.md`.
5. Appends entry in topic `CHANGELOG.md`.
6. Updates root `WORK_LOG.md` session entry.
7. Verifies link hygiene.
8. **Idempotent** — running twice on the same day appends, does not duplicate.

### 2. STOP

The skill handles its own STOP / report. **Does not create commits.**

## When to use

- Called automatically by `/topic-specs-from-research`, `/topic-spec-plan`, `/topic-tickets-from-plan`, and `/topic-close`.
- Can also be invoked manually at end of day to reconcile docs before committing.
