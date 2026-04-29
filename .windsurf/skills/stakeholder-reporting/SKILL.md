---
name: stakeholder-reporting
description: Generate a non-technical stakeholder update for a topic — friendly, plain-English, follows the stakeholder-report template. Manual-only. Never auto-triggered by the lifecycle.
---

# Stakeholder Reporting Skill

## Persona

**MANDATORY first action:** Load the Communicator persona by reading `.windsurf/personas/communicator.md`. Adopt it for the duration of this skill. The 15-year-old test, anti-jargon discipline, and outcome-translation principles all live there.

## Inputs

- **TOPIC_SLUG** (optional) — topic folder under `docs/`. Default: auto-detect most recently active topic by `WORK_LOG.md` newest session.
- **PERIOD** (optional) — human-friendly period covered (e.g., `"April 16 – April 28, 2026"`, `"this week"`). Default: ask the user.
- **AUDIENCE** (optional) — short label (e.g., `"non-technical partners"`, `"investors"`, `"general public"`). Default: ask the user.
- **TONE** (optional) — `friendly-neutral` | `15yo-test` | `investor-formal` | `customer-update`. Default: `15yo-test` (project standard).
- **CONTACT** (optional) — contact line for "Questions?" section.

## Output

A single Markdown file:

```
docs/{TOPIC_SLUG}/stakeholder-reports/{YYYY-MM-DD}__{short_audience_slug}.md
```

Examples:
- `docs/{TOPIC_SLUG}/stakeholder-reports/{YYYY-MM-DD}__non-technical-partners.md`
- `docs/{TOPIC_SLUG}/stakeholder-reports/{YYYY-MM-DD}__investors.md`

Uses `.windsurf/_templates/stakeholder-report-template.md`. The file MUST start with the standard backlink `[← Back to README](../README.md)`.

**Does NOT** touch CHANGELOG, WORK_LOG, SESSION-CHANGES, or create commits.

## Procedure

### 1. Gather inputs

If any of `PERIOD`, `AUDIENCE`, `CONTACT` was not supplied, ASK the user once with a single consolidated question. Do not guess.

Confirm `TOPIC_SLUG` by reading `docs/WORK_LOG.md` and pointing at the topic the latest session entry belongs to. If multiple topics had recent activity, ask the user.

### 2. Read source material

Read (the "facts" the report is built from):

- `docs/{TOPIC_SLUG}/README.md` — current status, phase table.
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — entries within `PERIOD`.
- `docs/{TOPIC_SLUG}/SESSION-CHANGES-*.md` files within `PERIOD` (newest first).
- Each completion report (`reports/{TICKET_ID}__*__REPORT.md`) for tickets closed in `PERIOD`.
- `docs/WORK_LOG.md` — session entries for this topic within `PERIOD`.
- Any prior stakeholder reports in `docs/{TOPIC_SLUG}/stakeholder-reports/` (so the new one extends rather than repeats).

### 3. Translate, don't copy

Build a mental model of:

- **What was actually done** — bullet list of outcomes (NOT commit messages).
- **What this means for users / business / team** — separate angle for each named audience.
- **Where the project stands** — count of phases done vs total; pick a single representative percentage.
- **What ships next** — the very next ticket and a one-liner on the one after.

Now translate every bullet into plain English using the Communicator persona's discipline (15-year-old test, comparisons over numbers, one verb per bullet, no jargon).

### 4. Choose visuals

Pick visuals that fit the audience:

- **Progress bar** (`▰▰▱▱▱▱▱`) — always include; single most-read element.
- **Mermaid Gantt / timeline** — only if audience is OK with a small diagram on a phone screen; skip for ultra-non-technical and replace with a simple "Done / In Progress / Later" bullet list.
- **By-the-numbers table** — pick 3-5 metrics; every metric needs a plain-English meaning column.

### 5. Draft the report

Use `.windsurf/_templates/stakeholder-report-template.md` as the starting shape. Keep ALL section headings (TL;DR, What We Did, What This Means For You, Journey So Far, By the Numbers, What's Coming Next, Glossary, Questions?). It's OK to leave a section short, but do not delete sections — they help the reader scan.

Save to `docs/{TOPIC_SLUG}/stakeholder-reports/{YYYY-MM-DD}__{short_audience_slug}.md`.

If `stakeholder-reports/` doesn't exist, create it.

### 6. Self-review (15-year-old test)

Before reporting back, re-read the draft and run the Communicator persona's self-review checklist (15-year-old test, jargon check, "what this means for you" answers "why should I care", progress bar / diagram consistency, plain-English meaning column, real contact). If any check fails, fix.

### 7. Link from README (optional)

Ask the user whether to add a link to this report from `docs/{TOPIC_SLUG}/README.md` under a "Stakeholder updates" section. If yes, add it. If they prefer out-of-band, skip.

## Final Report

```
✅ Stakeholder Report Generated

📄 File: docs/{TOPIC_SLUG}/stakeholder-reports/{YYYY-MM-DD}__{audience_slug}.md
📅 Period: {PERIOD}
👥 Audience: {AUDIENCE}
🧪 Tone: {TONE}

📋 Sections written:
- 30-Second Version
- What We Did This Period ({N} bullets)
- What This Means for You ({N} angles)
- The Journey So Far (progress: {X}/{Y} phases, {Z}%)
- By the Numbers ({N} metrics)
- What's Coming Next (next: {ticket / phase})
- Glossary ({N} terms)
- Questions? (contact: {CONTACT})

🎯 Next:
- Review the draft and tweak wording for the actual audience.
- Send / share via {channel}.
- This file was NOT committed — commit manually if you want it in the repo.
```

## Rules

- **NEVER auto-run.** Always invoked manually via `/stakeholder-report`.
- **Output is single-file.** Do not produce companion files.
- **No commits.**
- **No production data leaks.** Never paste raw error messages, customer data, internal URLs, or unredacted credentials.
- **No invented progress.** Every claim must trace to a CHANGELOG / SESSION-CHANGES / completion report entry. If uncertain, cut it.
- **Backlink mandatory.** First content line is the standard backlink.
- **15-year-old test by default.** Override only if the user explicitly requests a more technical tone.
