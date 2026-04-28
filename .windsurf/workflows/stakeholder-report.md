---
description: Manually generate a non-technical stakeholder report for a topic using the stakeholder-report template
---

# STAKEHOLDER REPORT — Friendly, Non-Technical Update

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

**Manual workflow.** Run this when you want to share progress with people who
are NOT engineers — clients, partners, family, investors, the team's
non-technical members. The output is one Markdown file written in plain,
friendly language that a curious 15-year-old could follow.

This workflow is **never** triggered automatically. It is not embedded in
`/topic-close`. The user invokes it explicitly when they want a fresh
stakeholder update.

It does NOT touch CHANGELOG, WORK_LOG, SESSION-CHANGES, or any commit. The
generated file is a standalone artifact saved under
`docs/{topic-slug}/stakeholder-reports/`.

## Inputs

- **TOPIC_SLUG** (optional): topic folder under `docs/`. Default: auto-detect
  the most recently active topic by `WORK_LOG.md` newest session.
- **PERIOD** (optional): human-friendly period covered (e.g.
  `"April 16 – April 28, 2026"`, or `"this week"`, or `"since the last update"`).
  Default: ask the user.
- **AUDIENCE** (optional): short label describing who reads this (e.g.
  `"non-technical partners"`, `"investors"`, `"general public"`,
  `"team's product folks"`). Default: ask the user.
- **TONE** (optional): one of `friendly-neutral` | `15yo-test` | `investor-formal`
  | `customer-update`. Default: `15yo-test` (the project's standard — every
  paragraph must pass the "could a curious 15-year-old follow this?" test).
- **CONTACT** (optional): a contact line for the "Questions?" section.

## Steps

### 1. Gather Inputs

If any of `PERIOD`, `AUDIENCE`, `CONTACT` were not supplied, ASK the user
once with a single consolidated question. Do not guess.

Confirm `TOPIC_SLUG` by reading `docs/WORK_LOG.md` and pointing at the topic
the latest session entry belongs to. If multiple topics had recent activity,
ask the user which one this report covers.

### 2. Read Source Material

// turbo

Read these files (the "facts" the report will be built from):

- `docs/{TOPIC_SLUG}/README.md` — current status, phase table
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — all entries within `PERIOD`
- `docs/{TOPIC_SLUG}/SESSION-CHANGES-*.md` files within `PERIOD` (newest first)
- Each completion report (`reports/{TICKET_ID}__*__REPORT.md`) for tickets
  closed in `PERIOD`
- `docs/WORK_LOG.md` — session entries for this topic within `PERIOD`
- Any prior stakeholder reports in
  `docs/{TOPIC_SLUG}/stakeholder-reports/` (so the new one extends rather
  than repeats the last one)

### 3. Translate, Don't Copy

Build a mental model of:

- **What was actually done** — bullet list of outcomes (not commit messages)
- **What this means for users / business / team** — separate angle for each audience the user named
- **Where the project stands** — count of phases done vs total; pick a single
  representative percentage
- **What ships next** — the very next ticket and a one-line preview of the
  one after

Now translate every bullet into plain English. The "15-year-old test":

- Replace every technical term with a real-world analogy or define it inline
- Strip version numbers, framework names, hash IDs, and acronyms — unless
  unavoidable, in which case put them in the **Glossary**
- Prefer comparisons over numbers (e.g. "renamed every chapter heading in a
  110-chapter book" beats "migrated 105 templates")
- One verb per bullet, present tense
- No more than ~3 sentences per section before adding a sub-bullet or visual

### 4. Choose the Visuals

Pick visuals that fit the audience:

- **Progress bar** — `▰▰▱▱▱▱▱` style, always include this; it's the single
  most-read element of the report
- **Mermaid Gantt or timeline** — only if the audience is OK with a small
  diagram on a phone screen; skip for ultra-non-technical audiences and
  replace with a simple bullet list "Done / In Progress / Later"
- **By-the-numbers table** — pick 3–5 metrics, every metric needs a
  plain-English meaning column

### 5. Draft the Report

Use `.windsurf/_templates/stakeholder-report-template.md` as the starting
shape. Keep ALL section headings (TL;DR, What We Did, What This Means For You,
Journey So Far, By the Numbers, What's Coming Next, Glossary, Questions?). It
is OK to leave a section short, but do not delete sections — they help the
reader scan.

Save to:

```
docs/{TOPIC_SLUG}/stakeholder-reports/{YYYY-MM-DD}__{short_audience_slug}.md
```

Examples:

- `docs/app-update-v21/stakeholder-reports/2026-04-28__non-technical-partners.md`
- `docs/app-update-v21/stakeholder-reports/2026-04-28__investors.md`

If `stakeholder-reports/` doesn't exist yet, create it.

The file MUST start with the `[← Back to README](../README.md)` backlink as
per `docs/LINKING-RULES.md`.

### 6. Self-Review (15-Year-Old Test)

Before reporting back, re-read the draft and check:

- [ ] Could a curious 15-year-old read every section without stopping?
- [ ] Are there any unexplained acronyms or version numbers in the body?
      (If yes, either remove them or move them to the Glossary.)
- [ ] Does the **What This Means for You** section actually answer "why
      should I care", not just repeat what was done?
- [ ] Is the progress bar consistent with the Mermaid diagram (if any)?
- [ ] Are all numbers in the **By the Numbers** table also given a
      plain-English meaning column?
- [ ] Is the Questions? section pointing at a real contact?

If any check fails, fix it. Don't ship until all checks pass.

### 7. Link from README (optional)

Ask the user whether to add a link to this stakeholder report from
`docs/{TOPIC_SLUG}/README.md` under a "Stakeholder updates" section. If they
say yes, add the link. If they prefer to keep stakeholder reports
out-of-band, skip this step.

### 8. Report

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
- Review the draft and tweak wording for the actual audience
- Send / share via {channel}
- This file was NOT committed — commit manually if you want to keep it in
  the repo, or copy-paste the rendered output into an email / message
```

---

## Rules This Workflow Enforces

- **NEVER auto-run.** Always invoked manually.
- **Output is single-file.** Do not produce companion files.
- **No commits.** This workflow does not commit anything.
- **No production data leaks.** Never paste raw error messages, customer
  data, internal URLs, or unredacted credentials into the report.
- **No invented progress.** Every claim must trace to a CHANGELOG /
  SESSION-CHANGES / completion report entry. If something feels uncertain,
  cut it.
- **Backlink mandatory.** First content line is the `[← Back to README]` link.
- **15-year-old test by default.** Override only if the user explicitly asks
  for a more technical tone.

## Notes

- The output file lives in `docs/{TOPIC_SLUG}/stakeholder-reports/`, separate
  from the engineering-facing `report_YYYY-MM-DD.md` daily reports. The two
  serve different audiences and should not be confused.
- Multiple stakeholder reports per period are fine (e.g. one for partners,
  one for investors). Disambiguate via the `__{audience_slug}` suffix.
- For very small updates, a short stakeholder report (1 paragraph + progress
  bar) is fine. Keep it human-sized — no one reads a 5-page weekly update.
