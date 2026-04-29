---
description: Author the Execution Plan (the 6th spec) from the 5 conceptual specs. Does NOT generate ticket files.
---

# /topic-spec-plan — Author the Execution Plan

Thin entry point. Loads the `topic-spec-planning` skill (which loads `@Personas:spec-author` + `@Personas:planner`) and executes its full procedure.

## Inputs

- **TOPIC_SLUG** (required) — topic folder under `docs/`.

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/topic-spec-planning/SKILL.md` end-to-end with the input above. The skill:

1. Loads `@Personas:spec-author` (primary) + `@Personas:planner` (supporting).
2. Reads the 5 conceptual specs.
3. Pre-flight: confirms specs are stable.
4. Measures file sizes via `wc -l` for the Sizing Foundation.
5. Authors the Execution Plan (ticket inventory, dependency map, parallelization, sizing, scope contracts).
6. Runs the Execution-Plan quality gate.
7. **STOPs at user-review gate** for user confirmation.
8. Updates the topic README's Specs section.
9. Calls `session-logging`.

### 2. STOP

The skill handles its own STOP / report. Do not auto-route.

## After it finishes

Review the plan. Then run `/topic-tickets-from-plan`.
