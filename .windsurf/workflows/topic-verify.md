---
description: Verify topic ticket implementation against acceptance criteria and check for regressions.
---

# /topic-verify — Verify the Work

Thin entry point. Loads the `topic-verification` skill (which loads `@Personas:verifier`) and executes its full procedure.

## Inputs

- **TICKET_ID** (required) — e.g. `T-04`.
- **TOPIC_SLUG** (optional) — auto-detect from current branch / ticket ID.

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/topic-verification/SKILL.md` end-to-end with the inputs above. The skill:

1. Loads `@Personas:verifier`.
2. Reads ticket spec + implementation evidence.
3. Acceptance Criteria check (per AC, with file-cited evidence).
4. Guardrails check.
5. Side-effect check (call sites, build, tests).
6. Regression spot-checks.
7. Fixes issues by severity (Critical → Major → Minor).
8. Runs the verification quality gate.
9. Writes `{TICKET_ID}__*__VERIFICATION_REPORT.md` to `docs/{TOPIC_SLUG}/reports/`.

### 2. STOP

The skill handles its own STOP / report. No commits — that's `/topic-close`.

## After it finishes

Review the verification report. Then run `/topic-close {TICKET_ID}`.
