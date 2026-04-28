---
description: Turn topic research artifacts into canonical spec set and a ticket inventory
---

# TOPIC SPECS FROM RESEARCH — Research → Specs → Tickets

## 🌐 Project Override Hook

Before executing this workflow, check whether the host project provides an override.

**Path** (in the host project's workspace root, NOT this global repo):
`.windsurf/overrides/workflows/<same-filename-as-this-file>.md`

If present, apply its mode (frontmatter `mode:` OR first heading `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — run ONLY the override; ignore the steps below.
- **EXTEND** — run the steps below as written, then run the override as a final phase (or at the position it specifies).
- **MODIFY** — run the steps below, with the steps the override declares substituted / inserted at the matching positions.

No override → proceed as written. Ambiguous mode → **STOP and ask the user**.

---

> ⚠️ **MANDATORY EXECUTION PROTOCOL**
> This is a **strict step-by-step workflow**, NOT background context.
> - Execute steps **in order**, one at a time
> - **Do NOT skip steps** — every step exists for a reason
> - **Do NOT combine or optimize** steps unless explicitly allowed
> - **Do NOT treat this as context** to inform freeform work
> - After completing each step, move to the next step
> - If a step says to execute another workflow, **execute it fully** before continuing with this one
> - If you finished all steps here AND this workflow is called from parent workflow, get back to executing parent workflow steps.
> - If you finished all steps AND there are no parent workflow, mandatory is to stop and report to the user. Do not jump to "logical" next step!
>
> **Violation of this protocol undermines the entire development system.**

Turns research artifacts in a topic's `research/` folder (plus external research from Traycer or equivalent tools) into the **canonical 5-spec set** in `specs/` and a **ticket inventory** in `tickets/`.

**Input:** research artifacts (raw), partial investigation notes, external spec fragments.

**Output:**
1. Five (or more) spec files in `docs/{TOPIC_SLUG}/specs/` per `.windsurf/_templates/*-template.md`.
2. One ticket file per Execution Plan §6 entry in `docs/{TOPIC_SLUG}/tickets/`, per `ticket-template.md`.

## Inputs

- **TOPIC_SLUG** (required): the topic folder name under `docs/`
- **RESEARCH_REFS** (optional): list of external research file paths or
  `spec:UUID/UUID` refs to incorporate beyond what's in the topic's
  `research/` folder

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**

Read and internalize the skill file — you MUST follow ALL its behaviors and
rules throughout this entire workflow:

- Read `.windsurf/skills/spec-author/SKILL.md`

Do NOT proceed to Step 1 until you have read and understood the skill file.

### 1. Gather Research Context

// turbo

Read every file in the topic's `research/` folder (if it exists). Also read
any additional research artifacts supplied via `RESEARCH_REFS`.

Build an internal map of:

- **Problem statement** (explicit or implied) — for the Epic Brief
- **Current-state facts** (files, behaviors, data flows, library versions) —
  for the Codebase Investigation
- **Decision points** (open questions, chosen approaches, tradeoffs) — for
  the Architecture Spec's Decisions section
- **User flows touched** (existing and new) — for Core Flows
- **Files that will change** (new / modified / deleted) — for the
  Implementation Spec's file-level map

If the research is insufficient to populate any of the above, **stop and
report** to the user what's missing. Do not invent.

### 2. Read Topic Context

// turbo

Read:

- `docs/{TOPIC_SLUG}/README.md` — existing topic hub (may already carry partial
  scope definition)
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — history so far
- Any existing files in `docs/{TOPIC_SLUG}/specs/` — check whether prior
  specs already cover some of the canonical set (avoid duplication)

### 3. Verify Research Artifacts

For each claim that will appear in a spec, verify it against the actual codebase. Per the **spec-author skill**:

- **File paths**: `ls` / `find` to confirm every `file:` ref resolves.
- **Line counts**: `wc -l` on every file in the planned Implementation Spec §4.
- **Behaviors**: read the referenced file region and confirm research description matches.
- **External / library assumptions**: verify against `package.json` + `node_modules` source or official docs.

Produce a short **"Research Verification Notes"** of discrepancies found. Each becomes either a correction baked into the spec set (preferred) or an explicit "Open Question" for the user to resolve before ticket generation.

### 4. Author Epic Brief

Create `docs/{TOPIC_SLUG}/specs/Epic_Brief_—_{Topic Title}.md` using `.windsurf/_templates/epic-brief-template.md`. Follow the template structure.

Critical requirements:
- Keep it short (under ~200 lines). NO implementation detail.
- Explicit **Scope — IS / IS NOT** and **Success Criteria** (user-visible, measurable)
- **Constraints & Non-Functionals**, **Dependencies & Related Work**, **External References**

### 5. Author Codebase Investigation

Create `docs/{TOPIC_SLUG}/specs/Codebase_Investigation_(Raw_Detailed)_—_{...}.md`.

This is the **evidence file** — facts only, no architectural decisions. Copy / summarize verified current-state facts from Step 3 in raw-detailed structure: tables, file excerpts, observed data flows.

### 6. Author Architecture Spec

Create `docs/{TOPIC_SLUG}/specs/Architecture_Spec_(Summary___Tech_Plan)_—_{...}.md` using `.windsurf/_templates/architecture-spec-template.md`. Follow the template structure.

Critical requirements:
- Mermaid flowchart in **Design Summary**
- One subsection per non-obvious **Key Design Decision** with rationale and rejected alternatives
- Tables: Ownership Model, Change Propagation, Data Model
- Explicit **Out-of-Scope** and **Open Questions** sections
- Cross-reference Epic Brief and Codebase Investigation at the top

### 7. Author Core Flows

Create `docs/{TOPIC_SLUG}/specs/Core_Flows_—_{...}.md` using `.windsurf/_templates/core-flows-template.md`. Follow the template structure.

Critical requirements:
- Per flow: trigger + preconditions, numbered steps from user's perspective, ASCII/HTML wireframe (desktop; mobile if responsive matters), Mermaid sequence diagram, error/recovery paths
- Cross-flow concerns: keyboard navigation, a11y, responsive breakpoints, loading/empty/error states

### 8. Author Implementation Spec

Create `docs/{TOPIC_SLUG}/specs/Implementation_Spec_(Summary)_—_File-Level_Change_Map_for_{...}.md` using `.windsurf/_templates/implementation-spec-template.md`. Follow the template structure.

Critical requirements:
- Mermaid flowchart of the work order in **Implementation Summary**
- **File-Level Change Map** — NEW / MODIFIED / REMOVED tables with `file:` refs, measured or estimated line counts, change weight
- **Workstream Breakdown**, **Cross-Cutting Concerns** (migration / back-compat, tests, docs), **Verification Strategy**, **Open Questions**
- Every claim must be traceable to the Architecture Spec or Codebase Investigation

### 9. Author Execution Plan

Create `docs/{TOPIC_SLUG}/specs/Execution_Plan_—_Ticket_Breakdown,_Sizing_&_Dependency_Map.md` using `.windsurf/_templates/execution-plan-template.md`. Follow the template structure.

Critical requirements:
- **File Inventory** with line counts measured via `wc -l` (not estimated)
- **Ticket Dependency Map** (Mermaid graph) and **Parallelization Strategy** (table)
- **Sizing Summary** — every ticket under ~500 lines changed OR justified exception
- **Ticket Inventory** (table — direct input to Step 10)
- **Per-Ticket Scope Contracts** — in/out of scope, key files, acceptance-criteria seeds, verification seeds, per ticket
- **Phase Structure** (if applicable), **Consumer / Migration Map** (if applicable), **Risks & Open Questions**

### 10. Verify Spec Set

Run the **Quality Gate** from `.windsurf/skills/spec-author/SKILL.md`:

- [ ] All 5 canonical specs exist and are non-empty
- [ ] Each spec references its upstream specs at the top
- [ ] Every `file:` reference resolves to a real path
- [ ] All line counts in Execution Plan §2 are measured (not estimated)
- [ ] Every ticket in Execution Plan §6 has dependencies listed
- [ ] Every ticket has acceptance-criteria seeds in §7
- [ ] No architectural decisions live in the Implementation Spec or
      Execution Plan
- [ ] Sizing summary shows every ticket under the budget or justifies the
      exception

If any gate fails, fix before proceeding to Step 11.

### 11. Present Spec Set to User for Confirmation

Stop and report to the user with:
- ✅ topic slug + paths of all 5 spec files created
- 🎫 ticket inventory — each ticket's `{TICKET_ID}: {title}` with risk 🟢/🟡/🔴 and approximate line count
- 🔗 dependency tree or summary from the Execution Plan
- ❓ open questions surfaced during spec authoring
- Closing prompt: `Proceed to ticket generation? (yes / yes with edits / no — pause for review)`

**WAIT for user decision before Step 12.**

If the user requests edits (e.g. "split T-A into T-A + T-A2"), apply them in the Execution Plan and re-run Step 10 before looping back to this step.

### 12. Generate Ticket Files

For each entry in Execution Plan §6:

1. Create `docs/{TOPIC_SLUG}/tickets/{TICKET_ID}__{short_slug}.md` using `.windsurf/_templates/ticket-template.md`.
2. Source ticket content from the upstream specs:
   - **Scope & Objective**: Execution Plan §7 scope contract + Epic Brief scope
   - **References**: links to all 5 upstream specs + §3 dependencies
   - **Detailed Steps**: Implementation Spec §4 rows assigned to this ticket + applicable Architecture Spec decisions
   - **Guardrails**: Execution Plan §10 risks + cross-cutting concerns flagged for this ticket
   - **Acceptance Criteria + Verification**: §7 seeds, expanded to binary-verifiable assertions and copy-pastable commands per the ticket template

Follow the ticket template's **Detailed Steps** rule strictly: every step names the file, exact change, and rationale.

### 13. Update Topic README

Update `docs/{TOPIC_SLUG}/README.md`:

- Add a **Specs** section (if not present) linking all 5 spec files
- Add a **Tickets & Status** table listing every generated ticket with status
  `⏳ Planned`

### 14. Call /log-session

Execute `/log-session` fully to update SESSION-CHANGES, topic CHANGELOG, and
root WORK_LOG to reflect the spec-authoring + ticket-generation session.

### 15. Final Report

```
✅ Specs + Tickets Generated

📁 Topic: {TOPIC_SLUG}
📄 5 canonical specs + ticket inventory
🎫 {N} tickets generated in docs/{TOPIC_SLUG}/tickets/

🎯 Next:
- /kickoff {first_ticket_id} — start first ticket
- or review tickets before starting: docs/{TOPIC_SLUG}/tickets/
```

---

## Error Handling

| Error | Action |
|-------|--------|
| Research folder empty or missing | Ask user for `RESEARCH_REFS` or instructions |
| `file:` ref in research does not resolve | Flag in Step 3 "Research Verification Notes"; do not include in specs |
| Line counts cannot be measured (new file) | Estimate and mark "~" with a note in the Execution Plan §2 |
| Quality gate fails | Fix in-place; if gate cannot be passed, stop and report |
| User rejects spec set in Step 11 | Apply requested edits, re-verify, re-ask |

## Notes

- Runs AFTER initial research (via `/topic-research` or external tools like Traycer).
- Does NOT implement code — it only produces specs + tickets. Implementation happens via `/topic-implement` per ticket.
- Always uses `@spec-author` skill mindset throughout.
- For trivial topics (1 ticket, <200 lines), the Execution Plan may be degenerate (1-row inventory) but all 5 canonical specs should still exist — each serves a distinct audience.
