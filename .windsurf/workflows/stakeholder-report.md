---
description: Generate a non-technical stakeholder update for a topic — friendly, plain-English, follows the stakeholder-report template. Manual-only. Never auto-triggered by the lifecycle.
---

# /stakeholder-report — Friendly, Non-Technical Update

Thin entry point. Loads the `stakeholder-reporting` skill (which loads `@Personas:communicator`) and executes its full procedure.

**Manual workflow.** Never triggered automatically by the topic lifecycle.

## Inputs

- **TOPIC_SLUG** (optional) — auto-detect most recently active topic if not provided.
- **PERIOD** (optional) — e.g. `"April 16 – April 28, 2026"`, `"this week"`. Default: ask the user.
- **AUDIENCE** (optional) — e.g. `"non-technical partners"`, `"investors"`. Default: ask the user.
- **TONE** (optional) — `friendly-neutral` | `15yo-test` | `investor-formal` | `customer-update`. Default: `15yo-test`.
- **CONTACT** (optional) — for the "Questions?" section.

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/stakeholder-reporting/SKILL.md` end-to-end with the inputs above. The skill:

1. Loads `@Personas:communicator`.
2. Gathers inputs (asks user for any missing required fields).
3. Reads source material (CHANGELOG, SESSION-CHANGES, completion reports, WORK_LOG, prior stakeholder reports).
4. Translates technical → plain English (Communicator's discipline: 15-year-old test, comparisons over numbers, no jargon).
5. Chooses visuals (progress bar always; Mermaid only if audience accepts).
6. Drafts the report using `.windsurf/_templates/stakeholder-report-template.md`.
7. Self-reviews against the 15-year-old test.
8. Saves to `docs/{TOPIC_SLUG}/stakeholder-reports/{YYYY-MM-DD}__{audience_slug}.md`.

### 2. STOP

The skill handles its own STOP / report. **Never commits, never touches CHANGELOG / WORK_LOG / SESSION-CHANGES.**

## After it finishes

- Review the draft and tweak wording for the actual audience.
- Send / share via your channel of choice.
- The file was NOT committed — commit manually if you want it in the repo.
