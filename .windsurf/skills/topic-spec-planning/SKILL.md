---
name: topic-spec-planning
description: Author the Execution Plan (the 6th spec) from the 5 conceptual specs — ticket inventory, sizing budget, dependency map, parallelization strategy, per-ticket scope contracts. Does NOT generate ticket files (that's topic-tickets-authoring).
---

# Topic Spec Planning Skill

## Persona

**MANDATORY first action:** Load the Spec Author persona by reading `.windsurf/personas/spec-author.md`. Use the Planner persona as a supporting lens by also reading `.windsurf/personas/planner.md` for decomposition discipline.

## Inputs

- **TOPIC_SLUG** (required) — topic folder under `docs/`.

## Output

One spec file:
- `docs/{TOPIC_SLUG}/specs/Execution_Plan.md` — using `.windsurf/_templates/execution-plan-template.md`.

Plus an updated topic README that links the Execution Plan in its Specs section.

## Procedure

### 1. Read the 5 conceptual specs

Read all 5 from `docs/{TOPIC_SLUG}/specs/`:

- `Epic_Brief_—_*.md`
- `Codebase_Investigation_(Raw_Detailed)_—_*.md`
- `Architecture_Spec_(Summary___Tech_Plan)_—_*.md`
- `Core_Flows_—_*.md`
- `Implementation_Spec_(Summary)_—_*.md`

Build internal map of:
- **Workstreams** from Implementation Spec's Workstream Breakdown.
- **File-level changes** from the Change Map (NEW / MODIFIED / REMOVED).
- **Cross-cutting concerns** (migration, back-compat, tests, docs).
- **Open questions** from Architecture Spec — may translate to ticket-level risks.
- **External constraints** from Epic Brief affecting sizing or sequencing.

If any of the 5 specs is missing or empty, **STOP** — user must complete `/topic-specs-from-research` first.

### 2. Pre-flight: confirm specs are stable

Before authoring, the 5 specs must be stable:

- [ ] Open Questions in Architecture Spec are **resolved** or **explicitly carried into ticket-level risks** (not "we'll figure it out later").
- [ ] Implementation Spec's Change Map covers all in-scope files from Architecture Spec (no gaps).
- [ ] No `TODO` / `TBD` / `?` placeholders in spec bodies (those belong only in dedicated Open Questions sections).

If any check fails, **STOP** and report — user loops back to `/topic-specs-from-research` to close the gaps.

### 3. Measure file sizes (the Sizing Foundation)

For every file in the Implementation Spec's Change Map:

- **MODIFIED / REMOVED files**: run `wc -l`. Record actual line count.
- **NEW files**: estimate from Implementation Spec description; mark with `~` and a brief note.

Persist as the **File Inventory** for Execution Plan §2.

This step is **gating** — without measured (not estimated) line counts for existing files, the sizing rollup in §6 cannot be defended.

### 4. Author the Execution Plan

Create `docs/{TOPIC_SLUG}/specs/Execution_Plan.md` using `.windsurf/_templates/execution-plan-template.md`. Critical sections (per template):

- §1 — **Cross-references** to all 5 conceptual specs at top.
- §2 — **File Inventory** with `wc -l` line counts (not estimated for existing files).
- §3 — **Workstream Decomposition** — Implementation Spec workstreams → ticket breakdown.
- §4 — **Ticket Dependency Map** (Mermaid graph) — every ticket lists upstream dependencies.
- §5 — **Parallelization Strategy** (table) — which tickets can run in parallel and which must serialize.
- §6 — **Sizing Summary** — every ticket under ~500 lines changed OR justified exception with rationale.
- §7 — **Per-Ticket Scope Contracts** — for each ticket: in/out of scope, key files, acceptance-criteria seeds, verification seeds, risk level.
- §8 — **Phase Structure** if applicable.
- §9 — **Consumer / Migration Map** if applicable.
- §10 — **Risks & Open Questions** (carried from Architecture Spec + ticket-level).

Every ticket entry in §7 must be specific enough that `topic-tickets-authoring` can produce a per-file ticket spec without re-reading the conceptual specs.

### 5. Quality Gate (Execution Plan only)

- [ ] Cross-references at top resolve to all 5 conceptual specs.
- [ ] File Inventory uses measured (`wc -l`) line counts for existing files; NEW files marked `~` with note.
- [ ] Every ticket has dependencies listed (or "none" explicitly).
- [ ] Every ticket has acceptance-criteria seeds in §7.
- [ ] Sizing summary shows every ticket under budget OR has explicit per-ticket exception with reason.
- [ ] Dependency graph has no cycles (verify by tracing).
- [ ] No architectural decisions live in the Execution Plan — they belong in Architecture Spec; if a new one surfaced during planning, STOP and add to Architecture Spec first.
- [ ] Open questions section is non-empty if any remain, or explicitly "None" if all closed.

If any gate fails, fix before user-review.

### 6. User-review gate

Stop and report:

- ✅ Path of new Execution Plan file.
- 🎫 Ticket inventory — `{TICKET_ID}: {title}` with risk 🟢/🟡/🔴 and approximate line count.
- 🔗 Dependency tree summary from §4–§5.
- 📐 Sizing summary from §6 — total lines, per-phase if applicable, exceptions.
- ❓ Open questions surfaced during planning.
- Closing prompt: `Proceed to /topic-tickets-from-plan? (yes / yes with edits / no — pause for review)`.

**WAIT** for user decision. If user requests edits (split a ticket, merge two, reduce scope), apply and re-run Step 5 before re-asking.

### 7. Update Topic README — Specs section

In `docs/{TOPIC_SLUG}/README.md`:
- Ensure Execution Plan is linked alongside the 5 conceptual specs in the **Specs** section.
- Do **NOT** create the Tickets table here — that's `topic-tickets-authoring`'s job.

### 8. Call session-logging

Execute `session-logging` skill to update SESSION-CHANGES, topic CHANGELOG, root WORK_LOG.

## Final Report

```
✅ Execution Plan Authored — {TOPIC_SLUG}

📄 Spec: docs/{TOPIC_SLUG}/specs/Execution_Plan.md

🎫 Tickets planned: {N}
📐 Sizing: {total lines changed}; {count} tickets, {count} exceptions
🔗 Dependencies: {brief graph summary}
❓ Open questions: {count}

🎯 Next: /topic-tickets-from-plan — generate per-ticket files based on this plan
```

Then **STOP**.

## Error Handling

| Error | Action |
|-------|--------|
| Any of the 5 conceptual specs missing | STOP — user must run `/topic-specs-from-research` first. |
| Conceptual specs have unresolved Open Questions | STOP — user closes them in Architecture Spec, then re-runs. |
| Quality gate fails | Fix in-place; if a gate cannot pass, stop and report. |
| User rejects plan at gate | Apply edits, re-verify, re-ask. |
| New architectural decision surfaces during planning | STOP — add to Architecture Spec first, then resume. |
