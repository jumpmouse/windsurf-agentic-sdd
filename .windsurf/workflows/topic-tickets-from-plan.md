---
description: Generate per-ticket spec files from the Execution Plan, then update the topic README ticket table.
---

# /topic-tickets-from-plan — Generate Ticket Files

Thin entry point. Loads the `topic-tickets-authoring` skill (which loads `@Personas:spec-author`) and executes its full procedure.

## Inputs

- **TOPIC_SLUG** (required) — topic folder under `docs/`.

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/topic-tickets-authoring/SKILL.md` end-to-end with the input above. The skill:

1. Loads `@Personas:spec-author`.
2. Reads Execution Plan + the 5 conceptual specs.
3. Pre-flight: confirms plan is stable.
4. Resolves ticket generation order from the dependency graph.
5. Generates one ticket file per Execution Plan §7 entry using `.windsurf/_templates/ticket-template.md`.
6. Updates topic README with the Tickets & Status table.
7. Runs the ticket-set quality gate.
8. Calls `session-logging`.

### 2. STOP

The skill handles its own STOP / report. Do not auto-route.

## After it finishes

Run `/topic-tickets-verify` to validate the generated tickets before implementation begins.
