---
description: Start working on a topic ticket — gather context, identify ticket, route to next action
---

# KICKOFF — Topic Entry Point

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

## Inputs

- **TOPIC_SLUG** : Folder name in docs, e.g. `app-update-v21`. if not provided, auto-detect from docs/CHANGELOG.md.
- **TICKET_ID** (OPTIONAL): e.g. `T-04`. If not provided, auto-detect from topic CHANGELOG.md.

## Project Overview

Universal entry point for any topic work in this project/workspace. All documentation lives inside `docs/{TOPIC_SLUG}/`.

## Docs Structure

```
docs/{TOPIC_SLUG}/
├── README.md              ← project overview, status table, links
├── CHANGELOG.md           ← all changes with dates, tickets, commits
├── report_YYYY-MM-DD.md   ← daily non-technical progress reports
├── specs/                 ← topic roadmap & guardrails
├── tickets/               ← ticket specs (the plan for each task)
└── reports/               ← per-ticket completion reports
```

## Lifecycle

```
/kickoff (this workflow — context + route)
  → /topic-research (@deep-investigator)   ← complex tickets only, user reviews
  → /topic-implement (@spec-implementer)   ← execute ticket spec
  → /topic-verify (@verification-fixer)    ← verify against acceptance criteria
  → /topic-close (@code-reviewer)          ← report, changelog, readme, commit
```

Important note: **the ticket spec IS the plan**. No separate planning phase.

## Steps

### 0. Confirm Execution Mode

// turbo
**MANDATORY — Do this FIRST before any other work.**

Print the following message to the user, exactly:
```
🔒 KICKOFF — STRICT EXECUTION MODE
Following mandatory step-by-step protocol. No steps will be skipped.
```

Then proceed to Step 1.

### 1. Detect Topic

// turbo
If TOPIC_SLUG was provided, use it and skip to Step 2.

If not, auto-detect:
- Read `docs/CHANGELOG.md` — find the **last entry** (most recent date, last topic heading)
- The topic heading format is `### [{TOPIC_TITLE}]({TOPIC_SLUG}/) — ...`
- Extract the TOPIC_SLUG from that link
- If `docs/CHANGELOG.md` doesn't exist or is empty, list subdirectories of `docs/` and pick the one with the most recent `CHANGELOG.md` modification
- If still ambiguous, ASK the user

Report:
```
📂 Topic detected: {TOPIC_SLUG} (from root CHANGELOG last entry)
```

### 2. Gather Topic Context

// turbo
Read these files to understand current state:
- `docs/{TOPIC_SLUG}/README.md` — status table, completed/pending tickets
- `docs/{TOPIC_SLUG}/specs/*.*` — all spec files for full topic context. Focus on latest work and next logical steps.
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — what was done and when; what was done last
- The most recent `docs/{TOPIC_SLUG}/report_*.md` — latest progress summary
- Any other relevant project files

Report:
```
📊 Project Status:
- Completed: {list of done ticket IDs}
- In Progress: {ticket ID if any}
- Next: {ticket ID}
- Branch: {current git branch}
```

### 3. Identify Target Ticket

If TICKET_ID was provided, use it.
If not, identify the next uncompleted ticket from README.md status table.

Read:
- `docs/{TOPIC_SLUG}/tickets/{TICKET_ID}__*.md` — the ticket spec (this is the plan)
- `docs/{TOPIC_SLUG}/README.md` — cross-reference status
- Dependency tickets' reports if referenced (e.g., if T-04 depends on T-03a, read its report)

Report:
```
🎫 Target: {TICKET_ID} — {title}
📋 Dependencies: {list or "none"}
🔴 Risk: {risk level from ticket}
📁 Files affected: {count from ticket spec}
```

### 4. Route to Next Action

Based on project state and ticket:

**Mode A — Fresh Ticket, Complex** (🔴 High risk, many files, new library APIs):
- Report: "This ticket is complex. Launching research first."
- Execute `/topic-research` with TICKET_ID
- Workflow **STOPS** after research — user reviews findings

**Mode B — Fresh Ticket, Straightforward** (🟢/🟡 risk, clear steps):
- Report: "Ticket is straightforward. Launching implementation."
- Execute `/topic-implement` with TICKET_ID

**Mode C — Resume** (work partially done):
- Check for existing report in `reports/`
- Check git log for related commits
- Check CHANGELOG.md for entries
- Determine current state and launch appropriate workflow:

| Current State | Next Action |
|---------------|-------------|
| Research done, not implemented | `/topic-implement` |
| Implementation done, not verified | `/topic-verify` |
| Verification passed, not closed | `/topic-close` |
| Report exists | Ticket already done — suggest next ticket |

Report decision:
```
🔄 Resuming: {TICKET_ID}
📋 Current state: {description}
🎯 Launching: {next workflow} — {reason}
```

---

## Notes

- The ticket spec IS the implementation plan — no separate `/topic-plan` needed
- For ticket complexity assessment, use the risk level in the ticket spec
- After research (Mode A), the user calls `/kickoff` again to resume
- When resuming, if state is ambiguous, ASK the user
