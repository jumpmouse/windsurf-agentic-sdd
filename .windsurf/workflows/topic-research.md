---
description: Interactive deep investigation. Standalone (no TOPIC_SLUG) → chat-only response. Topic-bound (with TOPIC_SLUG) → research artifacts in docs/{TOPIC_SLUG}/research/.
---

# /topic-research — Interactive Deep Investigation

Thin entry point. Loads the `topic-research` skill (which loads `@Personas:investigator`) and executes its full procedure.

## Inputs

- **PROBLEM** (required) — free-form problem / question.
- **TOPIC_SLUG** (optional) — folder name in `docs/`. Provided → topic-bound mode (writes artifacts). Absent → standalone mode (chat-only).

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/topic-research/SKILL.md` end-to-end with the inputs above. The skill:

1. Loads `@Personas:investigator` (and `@Personas:pattern-matcher` if pattern-matching language is detected).
2. Resolves output mode (standalone vs topic-bound).
3. Runs the investigation procedure (initial context → code investigation → gap categorization → self-resolve code-answerable gaps → user Q&A for genuinely unanswerable gaps → synthesis → output).
4. Honors the "No Stupid Questions" rule from the persona.
5. STOPs at completion (does not auto-route).

### 2. STOP

The skill handles its own STOP / report. Do not chain to the next workflow.

## After it finishes

- **Standalone mode:** findings in chat thread.
- **Topic-bound mode:** review `docs/{TOPIC_SLUG}/research/`, then run `/topic-specs-from-research`.
