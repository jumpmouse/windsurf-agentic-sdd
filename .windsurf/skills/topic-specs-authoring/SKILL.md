---
name: topic-specs-authoring
description: Author the 5 canonical conceptual specs (Epic Brief, Codebase Investigation, Architecture Spec, Core Flows, Implementation Spec) from research artifacts. Does NOT author the Execution Plan or generate ticket files — those are separate skills.
---

# Topic Specs Authoring Skill

## Persona

**MANDATORY first action:** Load the Spec Author persona by reading `.windsurf/personas/spec-author.md`. Adopt it for the duration of this skill.

## Inputs

- **TOPIC_SLUG** (required) — topic folder under `docs/`.
- **RESEARCH_REFS** (optional) — list of external research file paths (e.g. Traycer) or `spec:UUID/UUID` refs to incorporate beyond `docs/{TOPIC_SLUG}/research/`.

## Output

Five spec files in `docs/{TOPIC_SLUG}/specs/`:

1. `Epic_Brief_—_{Topic Title}.md` — using `.windsurf/_templates/epic-brief-template.md`
2. `Codebase_Investigation_(Raw_Detailed)_—_{...}.md` — evidence file, no decisions
3. `Architecture_Spec_(Summary___Tech_Plan)_—_{...}.md` — using `.windsurf/_templates/architecture-spec-template.md`
4. `Core_Flows_—_{...}.md` — using `.windsurf/_templates/core-flows-template.md`
5. `Implementation_Spec_(Summary)_—_File-Level_Change_Map_for_{...}.md` — using `.windsurf/_templates/implementation-spec-template.md`

This skill does **NOT** author the Execution Plan (that's `topic-spec-planning`) or generate ticket files (that's `topic-tickets-authoring`).

## Procedure

### 1. Gather research context

Read every file in `docs/{TOPIC_SLUG}/research/` plus any `RESEARCH_REFS`. Build internal map of:

- **Problem statement** → for the Epic Brief.
- **Current-state facts** (files, behaviors, library versions) → for the Codebase Investigation.
- **Decision points** (open questions, chosen approaches, trade-offs) → for the Architecture Spec.
- **User flows touched** → for Core Flows.
- **Files that will change** (NEW / MODIFIED / DELETED) → for the Implementation Spec.

If research is insufficient to populate any of the above, **STOP and report**. Remedy: re-run `/topic-research` with narrower focus. Do not invent facts.

### 2. Read topic context

Read:
- `docs/{TOPIC_SLUG}/README.md`
- `docs/{TOPIC_SLUG}/CHANGELOG.md`
- Any existing files in `docs/{TOPIC_SLUG}/specs/` (avoid duplication).

### 3. Verify research artifacts against code

Per the Spec Author persona's "ground every claim in evidence" rule:

- **File paths**: `ls` / `find` to confirm every `file:` ref resolves.
- **Line counts**: `wc -l` on every file in the planned Implementation Spec change map.
- **Behaviors**: read the referenced file region and confirm research description matches.
- **External / library assumptions**: verify against dependency manifest or official docs.

Produce short **Research Verification Notes** of discrepancies. Each becomes either a correction baked into the spec set (preferred) or an explicit Open Question carried into the Architecture Spec.

### 4. Author Epic Brief

Create using `.windsurf/_templates/epic-brief-template.md`. Critical:
- Short (<~200 lines). NO implementation detail.
- Explicit **Scope — IS / IS NOT** and **Success Criteria** (user-visible, measurable).
- **Constraints & Non-Functionals**, **Dependencies & Related Work**, **External References**.

### 5. Author Codebase Investigation

Evidence file. Facts only, no decisions. Copy / summarize verified current-state facts from Step 3 in raw-detailed structure: tables, file excerpts, observed data flows.

### 6. Author Architecture Spec

Use `.windsurf/_templates/architecture-spec-template.md`. Critical:
- Mermaid flowchart in **Design Summary**.
- One subsection per non-obvious **Key Design Decision** with rationale + rejected alternatives.
- Tables: Ownership Model, Change Propagation, Data Model.
- Explicit **Out-of-Scope** and **Open Questions** sections.
- Cross-reference Epic Brief and Codebase Investigation at the top.

### 7. Author Core Flows

Use `.windsurf/_templates/core-flows-template.md`. Per flow:
- Trigger + preconditions, numbered steps from user perspective, ASCII/HTML wireframe (desktop; mobile if responsive matters), Mermaid sequence diagram, error / recovery paths.
- Cross-flow concerns: keyboard navigation, a11y, responsive breakpoints, loading / empty / error states.

### 8. Author Implementation Spec

Use `.windsurf/_templates/implementation-spec-template.md`. Critical:
- Mermaid flowchart of work order in **Implementation Summary**.
- **File-Level Change Map** — NEW / MODIFIED / REMOVED tables with `file:` refs, measured/estimated line counts, change weight.
- **Workstream Breakdown**, **Cross-Cutting Concerns** (migration / back-compat, tests, docs), **Verification Strategy**, **Open Questions**.
- Every claim traceable to Architecture Spec or Codebase Investigation.

### 9. Quality Gate (5-spec subset)

- [ ] All 5 conceptual specs exist and are non-empty.
- [ ] Each spec references its upstream specs at the top.
- [ ] Every `file:` reference in any spec resolves to a real path.
- [ ] No architectural decisions live in the Implementation Spec (those belong in Architecture Spec).
- [ ] Open questions are explicitly listed in the Architecture Spec, not hidden in the Implementation Spec.

The Execution Plan + ticket inventory + sizing checks are NOT in this gate — those belong to `topic-spec-planning` and `topic-tickets-authoring`.

If any gate fails, fix in-place before user-review gate.

### 10. User-review gate

Stop and report to the user with:

- ✅ Topic slug + paths of all 5 spec files created.
- 📐 Architecture Spec key decisions (one-liner each).
- 🗂️ Implementation Spec file-level change map (counts: NEW / MODIFIED / REMOVED).
- ❓ Open questions surfaced.
- Closing prompt: `Proceed to /topic-spec-plan? (yes / yes with edits to a specific spec / no — pause for review)`.

**WAIT** for user decision. If the user requests edits, apply them and re-run Step 9 before re-asking.

### 11. Call session-logging

Execute the `session-logging` skill to update SESSION-CHANGES, topic CHANGELOG, and root WORK_LOG.

## Final Report

```
✅ 5 Conceptual Specs Generated — {TOPIC_SLUG}

📄 Specs: Epic Brief • Codebase Investigation • Architecture Spec • Core Flows • Implementation Spec

❓ Open questions carried forward: {count}

🎯 Next: /topic-spec-plan — author the Execution Plan based on these specs
```

Then **STOP**. Do not auto-route.

## Error Handling

| Error | Action |
|-------|--------|
| `research/` empty or missing | STOP — instruct user to run `/topic-research`, or supply `RESEARCH_REFS`. |
| `file:` ref in research does not resolve | Flag in Research Verification Notes; do not include in any spec. |
| Quality gate fails | Fix in-place; if a gate cannot be passed, stop and report. |
| User rejects spec at user-review gate | Apply edits, re-verify, re-ask. |
