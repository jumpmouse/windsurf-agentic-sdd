---
name: topic-tickets-authoring
description: Generate per-ticket spec files from the Execution Plan. Each ticket follows ticket-template.md and is executable without re-reading the conceptual specs. Updates the topic README's Tickets table.
---

# Topic Tickets Authoring Skill

## Persona

**MANDATORY first action:** Load the Spec Author persona by reading `.windsurf/personas/spec-author.md`. Adopt it for the duration of this skill.

## Inputs

- **TOPIC_SLUG** (required) — topic folder under `docs/`.

## Output

- One ticket file per Execution Plan §7 entry, in `docs/{TOPIC_SLUG}/tickets/`, named `{TICKET_ID}__{short_slug}.md`, using `.windsurf/_templates/ticket-template.md`.
- Updated `docs/{TOPIC_SLUG}/README.md` with the Tickets & Status table.

## Procedure

### 1. Read Execution Plan + required upstream specs

Read:
- `docs/{TOPIC_SLUG}/specs/Execution_Plan.md` — primary input; authoritative for ticket inventory, ordering, sizing, scope contracts.
- `docs/{TOPIC_SLUG}/specs/Implementation_Spec_(Summary)_—_*.md` — file-level changes per ticket.
- `docs/{TOPIC_SLUG}/specs/Architecture_Spec_(Summary___Tech_Plan)_—_*.md` — guardrails + key decisions per ticket.
- `docs/{TOPIC_SLUG}/specs/Epic_Brief_—_*.md` — scope boundaries.

If Execution Plan is missing or empty, **STOP** — user must run `/topic-spec-plan` first. If any conceptual spec is missing, **STOP** — refuse to generate without upstream context.

### 2. Verify plan is stable (pre-flight)

Quick check before mass generation:

- [ ] Execution Plan §7 (Per-Ticket Scope Contracts) has one entry per ticket in the inventory.
- [ ] Every ticket entry has: scope (in/out), key files, acceptance-criteria seeds, verification seeds, risk level.
- [ ] No ticket entry says "TBD" / "?" / "TODO" in the contract section.
- [ ] Sizing rollup in Execution Plan §6 matches ticket inventory count (no orphan tickets, no missing tickets).

If any check fails, **STOP** and report — user loops back to `/topic-spec-plan` to tighten the plan.

### 3. Resolve ticket generation order

Process tickets in **dependency order** (topologically sorted from Execution Plan §4 dependency graph): no-dependency tickets first; dependent tickets after their deps.

This order matters because each ticket may reference its dependency `{TICKET_ID}` in its References section — those refs must point to files that exist by read time.

If the dependency graph has a cycle, **STOP** — refuse to generate. This is a `/topic-spec-plan` quality-gate miss.

### 4. Generate each ticket file

For each ticket in dependency order, create `docs/{TOPIC_SLUG}/tickets/{TICKET_ID}__{short_slug}.md` using `.windsurf/_templates/ticket-template.md`.

**Source content from upstream specs:**

| Ticket section | Source |
|----------------|--------|
| Scope & Objective | Execution Plan §7 scope contract + Epic Brief scope boundaries |
| References | Links to all 5 conceptual specs + Execution Plan + dependency tickets per §4 |
| Pre-Implementation Context | Execution Plan §7 risks + Architecture Spec decisions relevant to this ticket |
| Detailed Steps | Implementation Spec §4 rows assigned to this ticket + applicable Architecture Spec decisions |
| Guardrails | Execution Plan §10 risks + cross-cutting concerns flagged for this ticket |
| Acceptance Criteria | §7 acceptance-criteria seeds, expanded to binary-verifiable assertions |
| Verification Steps | §7 verification seeds, expanded to copy-pastable commands per the ticket template |
| Notes | Open questions affecting this ticket; carry-overs to other tickets |

**Detailed Steps rule (strict):** every step names the exact file, the exact change (Before/After or transformation rule), and the rationale. No vague phrasing.

**Cross-link:** a ticket's References section MUST list file paths of all 5 conceptual specs + Execution Plan, plus `{TICKET_ID}` of every dependency ticket.

### 5. Update Topic README — Tickets table

In `docs/{TOPIC_SLUG}/README.md`:

Add (or refresh) the **Tickets & Status** table listing every generated ticket with status `⏳ Planned`. Columns:

- `Ticket` — `{TICKET_ID}`
- `Title` — short title
- `Risk` — 🟢 / 🟡 / 🔴 from Execution Plan §7
- `Depends on` — `{TICKET_ID(s)}` or `—`
- `Status` — `⏳ Planned`

Ensure Specs section already lists Execution Plan (from `/topic-spec-plan` Step 7) — if not, add it.

### 6. Quality Gate

- [ ] One ticket file exists per Execution Plan §7 entry — no orphans, no missing tickets.
- [ ] Every ticket has all template sections populated (no empty headings).
- [ ] Every ticket's References section links to all 5 conceptual specs + Execution Plan.
- [ ] Every dependency `{TICKET_ID}` in any ticket's References resolves to an existing ticket file in this same run.
- [ ] Topic README Tickets table count = generated ticket count.
- [ ] No `TBD` / `?` placeholders in any ticket's Detailed Steps, Acceptance Criteria, or Verification Steps.

If any gate fails, fix before final report.

### 7. Call session-logging

Execute `session-logging` skill to update SESSION-CHANGES, topic CHANGELOG, root WORK_LOG.

## Final Report

```
✅ Tickets Generated — {TOPIC_SLUG}

📁 Topic: docs/{TOPIC_SLUG}/
🎫 Tickets generated: {N}

  - {TICKET_ID}: {title} (risk {🟢/🟡/🔴})
  - ...

📐 Sizing rollup: {total lines}, {count} exceptions

🎯 Next:
- /topic-tickets-verify — quality gate on tickets ↔ Execution Plan + spec set
- After verify passes: /kickoff {first_ticket_id} → /topic-implement
```

Then **STOP**. Do not auto-route — user reviews tickets, then runs `/topic-tickets-verify`.

## Error Handling

| Error | Action |
|-------|--------|
| Execution Plan missing | STOP — user must run `/topic-spec-plan` first. |
| Any of the 5 conceptual specs missing | STOP — refuse to generate without upstream context. |
| Pre-flight Step 2 fails | STOP and report — user loops back to `/topic-spec-plan`. |
| Dependency graph has a cycle | STOP — flag as `/topic-spec-plan` quality-gate miss. |
| Quality gate (Step 6) fails | Fix in-place; if a gate cannot pass, stop and report. |
| Ticket scope larger than plan claimed | STOP — planning error; loop back to `/topic-spec-plan` to split. |
