# AI Workflows — How to Use

This document explains the available AI workflows for the SXE User Frontend project. These workflows automate the development process, documentation and review.

---

## How It Works — The Big Picture

Docs is organized into **topics** (similar to epics) and **tickets** (similar to user stories, named like: T-01, T-02, T-03, etc.). Each ticket describes a specific piece of work — what needs to change, which files are affected, and how to verify it's done correctly.

The AI assistant follows a structured process for each ticket:

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│   /kickoff                                          │
│   "Start working on a ticket"                       │
│                                                     │
│   Reads project status, picks the next ticket,      │
│   and decides what to do:                           │
│                                                     │
│     ┌─── Complex ticket? ──────────────────┐        │
│     │                                      │        │
│     ▼                                      ▼        │
│   /topic-research              /topic-implement     │
│   "Investigate first"            "Build it"         │
│                                                     │
│   Reads all affected code,       Follows the ticket │
│   checks assumptions,            step by step,      │
│   reports risks.                 tests each change. │
│                                                     │
│   ── User reviews findings ──          │            │
│   ── Then continues ─────────►         │            │
│                                        ▼            │
│                              /topic-verify          │
│                              "Check the work"       │
│                                                     │
│                              Verifies every         │
│                              acceptance criterion,  │
│                              fixes any issues.      │
│                                                     │
│                                        │            │
│                                        ▼            │
│                              /topic-close           │
│                              "Wrap it up"           │
│                                                     │
│                              Writes the report,     │
│                              updates changelog,     │
│                              suggests a commit.     │
│                                                     │
└─────────────────────────────────────────────────────┘
```

**For people who want to follow along:** after each step, the AI stops and shows you what it did. You can review, ask questions, or request changes before it moves on.

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

### `/topic-research` — Deep Investigation

**When to use:** Automatically launched by kickoff for complex, high-risk tickets. You can also request it manually.

**What it does:**
1. Reads the ticket specification thoroughly
2. Opens every file mentioned in the ticket and checks it against expectations
3. Maps which other files could be affected (the "blast radius")
4. Identifies risks and gaps in the ticket's plan
5. Reports findings and asks any questions before proceeding

**After it finishes:** You review the findings. If everything looks good, run `/kickoff` again to continue to implementation.

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
3. Updates the project changelog
4. Updates the project README status table
5. Creates or updates the daily progress report
6. Suggests a git commit message

**After it finishes:** You review the commit suggestion and push when ready.

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
| Investigate a complex ticket | `/topic-research T-04` |
| Implement a ticket | `/topic-implement T-04` |
| Verify implementation | `/topic-verify T-04` |
| Close and document a ticket | `/topic-close T-04` |
| Initialize project docs (run once) | `/init-project` |
| Create a new topic | `/topic-new` |
| Generate local setup guide | `/_generate-local-setup` |
| Generate sequence diagram | `/codemap-plantuml` |

---

## Where Things Live

```
.windsurf/
├── WORKFLOWS.md           ← this file
├── workflows/             ← active workflows
│   ├── kickoff.md
│   ├── topic-research.md
│   ├── topic-implement.md
│   ├── topic-verify.md
│   ├── topic-close.md
│   ├── topic-new.md
│   ├── init-project.md
│   ├── _generate-local-setup.md
│   └── codemap-plantuml.md
├── skills/                ← AI behavior profiles
│   ├── deep-investigator/ ← for research & debugging
│   ├── spec-implementer/  ← for disciplined implementation
│   ├── verification-fixer/← for verification & fixing
│   ├── code-reviewer/     ← for final review
│   ├── architect/         ← for design decisions
│   └── project-planner/   ← for planning
├── _templates/            ← document templates
│   ├── ticket-report-template.md
│   ├── daily-report-template.md
│   └── changelog-template.md
└── _archive/              ← archived workflows from previous project
```
