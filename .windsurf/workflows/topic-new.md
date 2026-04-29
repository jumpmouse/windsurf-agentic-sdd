---
description: Create a new topic folder under docs/ with standard structure (README, CHANGELOG, specs/, tickets/, reports/). Registers the topic in the docs hub and root WORK_LOG.
---

# /topic-new — Create a New Topic

Lightweight bootstrap: creates `docs/{TOPIC_SLUG}/` with the canonical folder structure, registers the topic in `docs/README.md`, and adds a topic section to `docs/WORK_LOG.md`. No skill — this is straightforward filesystem work.

## Inputs

- **TOPIC_SLUG** (required) — folder name, e.g. `performance-optimization`, `accessibility-audit`.
- **TOPIC_TITLE** (required) — human-readable title.
- **TOPIC_DESCRIPTION** (required) — one-line description.

## Topic Structure

```
docs/{TOPIC_SLUG}/
├── README.md              ← topic overview, status table, links
├── CHANGELOG.md           ← per-topic date-grouped changelog
├── specs/                 ← canonical 5-spec set + Execution Plan
├── tickets/               ← per-ticket spec files
├── reports/               ← completion + verification reports
├── research/              ← (conditional) raw research artifacts
└── stakeholder-reports/   ← (conditional) non-technical updates
```

Conditional folders materialize on demand — do NOT create empty.

## Steps

### 1. Validate

- Check `docs/{TOPIC_SLUG}/` does NOT already exist.
- If exists, report and ask user for a different slug.

### 2. Create folder structure

Create:
- `docs/{TOPIC_SLUG}/`
- `docs/{TOPIC_SLUG}/specs/`
- `docs/{TOPIC_SLUG}/tickets/`
- `docs/{TOPIC_SLUG}/reports/`

### 3. Create README.md

`docs/{TOPIC_SLUG}/README.md`:

```markdown
# {TOPIC_TITLE}

[← Back to root docs](../README.md)

{TOPIC_DESCRIPTION}

**Branch:** `{suggested branch name}`
**Status:** Planning

---

## Status

| Phase | Tickets | Status |
|-------|---------|--------|
| _Phases will be added as tickets are created_ | — | — |

---

## Quick Links

- [Changelog](CHANGELOG.md)
- [Root WORK_LOG](../WORK_LOG.md) — session-based project log

### Specs

_5 conceptual specs (added by `/topic-specs-from-research`):_

- Epic Brief
- Codebase Investigation
- Architecture Spec
- Core Flows
- Implementation Spec

_Execution Plan (added by `/topic-spec-plan`):_

- Execution Plan

### Tickets & Status

| Ticket | Title | Risk | Depends on | Status |
|--------|-------|------|------------|--------|
| _Tickets will be added as work is planned_ | — | — | — | — |
```

### 4. Create CHANGELOG.md

`docs/{TOPIC_SLUG}/CHANGELOG.md`:

```markdown
# Changelog — {TOPIC_TITLE}

[← Back to README](./README.md)

All notable changes for this topic. Each entry references the ticket spec, completion report, and git commit(s).

---

## {YYYY-MM-DD}

### Topic created
- Documentation structure initialized.
```

### 5. Register in docs hub

Update `docs/README.md` Topics table — append:

```markdown
| [{TOPIC_TITLE}]({TOPIC_SLUG}/) | {TOPIC_DESCRIPTION} | Planning |
```

### 6. Register in root WORK_LOG

Add new topic section at the top of `docs/WORK_LOG.md` using `.windsurf/_templates/work-log-entry-template.md`:

```markdown
# ({YYYY-MM-DD} — ongoing) {TOPIC_TITLE} ({TOPIC_SLUG}) — 🔄 In Progress

[Detailed plan](./{TOPIC_SLUG}/README.md)

{TOPIC_DESCRIPTION}

## Phases

- [ ] _Phases will be added as tickets are created_

## Sessions

_Session entries will be appended by `/log-session`._

---
```

If `docs/WORK_LOG.md` doesn't exist, STOP and recommend `/init-docs` first.

### 7. Report

```
✅ Topic Created: {TOPIC_TITLE}

📁 Structure:
- docs/{TOPIC_SLUG}/{README.md, CHANGELOG.md, specs/, tickets/, reports/}

📊 Registered in:
- docs/README.md (Topics table)
- docs/WORK_LOG.md (topic section with Phases + Sessions subheadings)

🎯 Next:
1. Run research: /topic-research (interactive) or external (Traycer)
2. Author 5 conceptual specs: /topic-specs-from-research
3. Author Execution Plan: /topic-spec-plan
4. Generate tickets: /topic-tickets-from-plan
5. Verify tickets: /topic-tickets-verify
6. Start working: /kickoff {first_ticket_id}
```

Then **STOP**.
