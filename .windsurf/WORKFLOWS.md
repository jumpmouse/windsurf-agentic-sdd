# AI Workflows — How to Use

This document explains the available AI workflows in this global Windsurf agentic-SDD setup. These workflows automate the development process, documentation, and review for any host project that adopts them — paths and project-specific commands are placeholders that resolve at the host project's level (via `docs/LOCAL_SETUP.md` or a project-local `.windsurf/overrides/`).

---

## How It Works — The Big Picture

Docs is organized into **topics** (similar to epics) and **tickets** (similar to user stories, named like: T-01, T-02, T-03, etc.). Each ticket describes a specific piece of work — what needs to change, which files are affected, and how to verify it's done correctly.

The AI assistant follows a structured process. `/kickoff` is the universal entry point and routes to one of two lifecycles depending on what's provided.

### Variant 1 — New work (PROBLEM provided)

Full end-to-end chain when starting from a new problem statement:

```
/kickoff
  → /topic-new                   create docs/{slug}/ folder structure
  → /topic-research              interactive investigation     (@deep-investigator)
  → /topic-specs-from-research   author 5-spec set + tickets   (@spec-author)
  → /topic-tickets-verify        verify tickets ↔ specs         (@verification-fixer)
  → /topic-implement             execute ticket spec           (@spec-implementer)
  → /topic-verify                verify implementation         (@verification-fixer)
  → /topic-close                 report, changelog, commit     (@code-reviewer)
```

> If research is produced **externally** (Traycer or equivalent), `/topic-research` is skipped and the chain enters at `/topic-specs-from-research` with `RESEARCH_REFS` linked. The rest of the chain is identical.

### Variant 2 — Resume / continue an existing topic

No PROBLEM provided. `/kickoff` auto-detects state and routes to the next pending workflow:

```
/kickoff   (state-based routing from README / CHANGELOG / git log)
  → /topic-research              if research is missing for the next ticket
  → /topic-implement             if research is done, implementation pending
  → /topic-verify                if implementation is done, verification pending
  → /topic-close                 if verification is done, close pending
```

**For people who want to follow along:** after each workflow, the AI stops and shows what it did. You review, ask questions, or request changes before it moves on.

---

## The Workflows

### `/kickoff` — Start Working

**When to use:** At the start of any work session, or when starting a new ticket.

**What it does:**
1. Reads the project status (which tickets are done, which is next)
2. Reads the topic specs and recent progress
3. Identifies the target ticket
4. Decides whether the ticket needs research first or can be implemented directly
5. Launches the appropriate next workflow

**How to use:**
- Type `/kickoff` — AI picks right topic and the next ticket automatically
- Type `/kickoff T-04` — AI works on ticket T-04 specifically

---

### `/topic-research` — Deep Investigation (Interactive)

**When to use:** As the second step of Variant 1 (new topic). Also auto-launched by kickoff for complex, high-risk existing tickets in Variant 2. Can be requested manually.

**What it does:**
1. Reads the topic / ticket context thoroughly
2. Opens every file mentioned and checks it against expectations
3. Maps which other files could be affected (the "blast radius")
4. Identifies risks, gaps, and open questions
5. Reports findings; **STOPS** for user review (interactive)

**Skill:** `@deep-investigator`

**After it finishes:** You review the findings. If everything looks good, run `/kickoff` again (Variant 1: continues to `/topic-specs-from-research`; Variant 2: continues to `/topic-implement`).

---

### `/topic-specs-from-research` — Author 5-Spec Set + Tickets

**When to use:** After research is complete (either via `/topic-research` or external tools like Traycer). Variant 1 only.

**What it does:**
1. Verifies every research claim against the actual codebase (Step 3 — integrated research-verification)
2. Authors the canonical 5-spec set: Epic Brief, Codebase Investigation, Architecture Spec, Core Flows, Implementation Spec
3. Authors the **Execution Plan** (the 6th spec) — ticket inventory, sizing, dependencies, parallelization
4. Runs an internal spec-set quality gate (Step 10)
5. Pauses for user review + approval (Step 11)
6. Generates per-ticket files in `docs/{slug}/tickets/` (Step 12)

**Skill:** `@spec-author`

**After it finishes:** Run `/topic-tickets-verify` to validate the generated tickets before implementation.

---

### `/topic-tickets-verify` — Pre-Implementation Ticket Quality Gate

**When to use:** Right after `/topic-specs-from-research` finishes generating tickets, before any `/topic-implement` runs.

**What it does:**
1. Reads the upstream specs (Execution Plan, Implementation Spec, Architecture, Core Flows, Epic Brief)
2. Verifies each generated ticket against those specs (file refs resolve, AC trace to upstream, dependencies match Execution Plan §4, sizing within budget)
3. Verifies the ticket SET as a whole (no orphaned files, no dependency cycles, sizing rollup matches plan)
4. Categorizes findings (Critical / Major / Minor) and proposes fixes
5. Writes `TICKETS_VERIFICATION_REPORT.md` in `docs/{slug}/reports/`

**Skill:** `@verification-fixer` (per skill description: *"verifying a plan against investigation findings and actual code"*)

**After it finishes:** PASS → `/topic-implement`. FAIL → fix tickets or re-run `/topic-specs-from-research` Step 12.

---

### `/topic-implement` — Build It

**When to use:** Automatically launched by kickoff, or manually after research is reviewed.

**What it does:**
1. Reads the ticket specification as the implementation plan
2. Checks that the codebase is in the expected state
3. Executes each step from the ticket, one at a time
4. Verifies the build passes after every few changes
5. Runs the full test suite at the end
6. Reports what was done and any issues found

**After it finishes:** You review the changes. Then run `/topic-verify` to verify.

---

### `/topic-verify` — Check the Work

**When to use:** After implementation is done, to verify everything meets the ticket's requirements.

**What it does:**
1. Checks every acceptance criterion from the ticket
2. Checks that no guardrails were violated (e.g., "don't rename Bootstrap classes in this ticket")
3. Traces side effects — did the changes break anything else?
4. Runs build and tests
5. Fixes issues by priority (critical first, then major, then minor)
6. Reports final verification status

**After it finishes:** You review. If clean, run `/topic-close` to close the ticket.

---

### `/topic-close` — Wrap It Up

**When to use:** After verification passes, to generate documentation and prepare the commit.

**What it does:**
1. Performs a final review of all changes
2. Writes a completion report for the ticket
3. Updates the topic CHANGELOG and the root WORK_LOG
4. Updates the topic README status table
5. Creates or updates the daily progress report
6. Creates code commits first, then a final docs commit (with hash backfill)

**Skill:** `@code-reviewer`

**After it finishes:** You review the commits and push when ready.

---

## Documentation Workflows

### `/log-session` — Reconcile Session Documentation

**When to use:** At the end of a working session to reconcile the documentation chain.

**What it does:**
1. Updates / creates `docs/{slug}/SESSION-CHANGES-{YYYY-MM-DD}.md` (today's session log)
2. Appends to the topic `docs/{slug}/CHANGELOG.md`
3. Appends to the root `docs/WORK_LOG.md` session list under the active topic
4. Idempotent — safe to re-run

Usually called automatically by `/topic-close`, but can be invoked manually.

---

### `/stakeholder-report` — Non-Technical Stakeholder Report

**When to use:** On demand, when a non-technical stakeholder update is needed for a topic.

**What it does:**
1. Reads the topic's reports / changelog / README
2. Generates a non-technical narrative report at `docs/{slug}/stakeholder-reports/{YYYY-MM-DD}.md`
3. Uses the stakeholder-report template

Never auto-generated by the topic lifecycle — always opt-in.

---

## Setup & Management Workflows

### `/init-project` — Initialize Project Documentation

**When to use:** Once, when setting up AI-assisted development on a project for the first time.

**What it does:**
1. Analyzes the codebase (tech stack, architecture, modules)
2. Creates the `docs/` hub with README, LOCAL_SETUP, and an initial code quality review
3. Registers any existing topic folders

### `/topic-new` — Create a New Topic

**When to use:** When starting a new area of work (e.g., "performance optimization", "accessibility audit").

**What it does:**
1. Creates a new folder under `docs/` with standardized structure (README, CHANGELOG, specs/, tickets/, reports/)
2. Registers the topic in `docs/README.md`

**How to use:** Type `/topic-new` and provide a slug, title, and description.

---

## Utility Workflows

These are standalone tools:

### `/_generate-local-setup` — Generate Local Setup Guide

Creates a `LOCAL_SETUP.md` file by investigating the project's build tools, dependencies, and configuration.

### `/codemap-plantuml` — Generate Sequence Diagram

Creates a PlantUML sequence diagram from a Windsurf Codemap mention. Useful for visualizing code execution paths.

---

## Quick Reference

| What you want to do | Command |
|---|---|
| Start working (auto-detect topic and next ticket) | `/kickoff` |
| Work on a specific ticket | `/kickoff T-04` |
| Create a new topic folder | `/topic-new` |
| Investigate a topic / complex ticket | `/topic-research` |
| Author 5-spec set + tickets from research | `/topic-specs-from-research` |
| Verify generated tickets before implementation | `/topic-tickets-verify` |
| Implement a ticket | `/topic-implement T-04` |
| Verify implementation | `/topic-verify T-04` |
| Close and document a ticket | `/topic-close T-04` |
| Reconcile session docs (CHANGELOG / WORK_LOG / SESSION-CHANGES) | `/log-session` |
| Generate non-technical stakeholder report | `/stakeholder-report` |
| Initialize project docs (run once) | `/init-project` |
| Generate local setup guide | `/_generate-local-setup` |
| Generate sequence diagram | `/codemap-plantuml` |

---

## Where Things Live

```
.windsurf/
├── WORKFLOWS.md                  ← this file
├── workflows/                    ← active workflows
│   ├── kickoff.md                ← universal entry point
│   ├── topic-new.md              ← create topic folder structure
│   ├── topic-research.md         ← interactive investigation (@deep-investigator)
│   ├── topic-specs-from-research.md ← 5-spec set + tickets (@spec-author)
│   ├── topic-tickets-verify.md   ← verify tickets pre-implementation (@verification-fixer)
│   ├── topic-implement.md        ← execute ticket spec (@spec-implementer)
│   ├── topic-verify.md           ← verify implementation (@verification-fixer)
│   ├── topic-close.md            ← close ticket: report + commit (@code-reviewer)
│   ├── log-session.md            ← reconcile session docs
│   ├── stakeholder-report.md     ← non-technical stakeholder report
│   ├── init-project.md           ← one-time project doc init
│   ├── _generate-local-setup.md  ← generate LOCAL_SETUP.md
│   └── codemap-plantuml.md       ← PlantUML diagram from Codemap
├── skills/                       ← AI behavior profiles
│   ├── architect/                ← system architecture / design trade-offs
│   ├── code-reviewer/            ← final review / PR-ready quality
│   ├── deep-investigator/        ← research / debugging / blast-radius mapping
│   ├── project-planner/          ← high-level planning / phase decomposition
│   ├── spec-author/              ← authoring 5-spec set + tickets
│   ├── spec-implementer/         ← disciplined plan-to-code execution
│   └── verification-fixer/       ← plan-vs-code + verification-report fixes
├── rules/                        ← always-on directives
│   ├── communication-during-work.md
│   ├── fixing-bugs.md
│   ├── investigation.md
│   └── pattern-matching-investigation.md
├── _templates/                   ← document templates
│   ├── epic-brief-template.md
│   ├── architecture-spec-template.md
│   ├── core-flows-template.md
│   ├── implementation-spec-template.md
│   ├── execution-plan-template.md
│   ├── ticket-template.md
│   ├── ticket-report-template.md
│   ├── verification-report-template.md
│   ├── session-changes-template.md
│   ├── work-log-entry-template.md
│   ├── stakeholder-report-template.md
│   ├── daily-report-template.md
│   └── changelog-template.md
└── _archive/                     ← archived / deprecated assets
```

**Override system:** every workflow / skill / rule starts with a **🌐 Project Override Hook** section that lets a host project replace / extend / modify it without renaming. Override files live in the host project at `.windsurf/overrides/{workflows,skills,rules}/`. See `.windsurf-project-overrides/README.md` for the convention.
