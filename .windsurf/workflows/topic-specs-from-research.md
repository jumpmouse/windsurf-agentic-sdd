---
description: Author the 5 canonical conceptual specs from research artifacts. Does NOT author the Execution Plan or generate ticket files.
---

# /topic-specs-from-research — Research → 5 Conceptual Specs

Thin entry point. Loads the `topic-specs-authoring` skill (which loads `@Personas:spec-author`) and executes its full procedure.

## Inputs

- **TOPIC_SLUG** (required) — topic folder under `docs/`.
- **RESEARCH_REFS** (optional) — list of external research file paths (e.g. Traycer) or `spec:UUID/UUID` refs.

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/topic-specs-authoring/SKILL.md` end-to-end with the inputs above. The skill:

1. Loads `@Personas:spec-author`.
2. Reads research artifacts + verifies claims against code.
3. Authors the 5 conceptual specs (Epic Brief, Codebase Investigation, Architecture Spec, Core Flows, Implementation Spec).
4. Runs the 5-spec quality gate.
5. **STOPs at user-review gate** for user confirmation.
6. After approval, calls `session-logging` to reconcile docs.

### 2. STOP

The skill handles its own STOP / report. Do not auto-route.

## After it finishes

Review the 5 specs. Then run `/topic-spec-plan`.
