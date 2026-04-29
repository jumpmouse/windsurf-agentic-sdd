---
description: Verify generated ticket specs against the Execution Plan and the 5-spec set before implementation begins.
---

# /topic-tickets-verify — Pre-Implementation Ticket Quality Gate

Thin entry point. Loads the `topic-tickets-verification` skill (which loads `@Personas:verifier`) and executes its full procedure.

## Inputs

- **TOPIC_SLUG** (required) — topic folder under `docs/`.
- **TICKET_ID** (optional) — if provided, verify only this ticket; otherwise verify ALL tickets.

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/topic-tickets-verification/SKILL.md` end-to-end with the inputs above. The skill:

1. Loads `@Personas:verifier`.
2. Reads upstream specs (5-spec set + Execution Plan).
3. Per-ticket verification (8 checks against upstream specs).
4. Cross-ticket verification (coverage, ordering, sizing rollup, no orphans).
5. Categorizes findings (Critical / Major / Minor).
6. Decision rule: any 🔴 Critical → STOP and ask user; only 🟡/🔵 → propose fixes inline.
7. Writes `TICKETS_VERIFICATION_REPORT.md` to `docs/{TOPIC_SLUG}/reports/`.

### 2. STOP

The skill handles its own STOP / report. Do not auto-edit ticket files for Critical findings.

## After it finishes

- **PASS** → `/kickoff {first_ticket_id}` → `/topic-implement`.
- **FAIL** → fix tickets, or re-run `/topic-tickets-from-plan` (or upstream `/topic-spec-plan` if the Execution Plan is wrong).
- **PASS-WITH-FOLLOWUPS** → review deferred items, then proceed.
