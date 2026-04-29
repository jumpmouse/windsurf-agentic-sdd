---
trigger: always_on
---

# Personas System — `@Personas:<name>` Convention

This system uses **personas** — reusable, mindset-focused profiles that shape how Cascade thinks about a task. Personas live in `.windsurf/personas/` as small markdown files (no procedural steps, just identity, expertise, principles, and heuristics).

Personas are the system's analog to Claude Code "subagents" — adapted for Windsurf's runtime.

## Two ways personas get loaded

### 1. Skill-driven (most common)

Every skill in this system declares a persona it strictly requires. When a skill is invoked, **before** executing any procedure step, the skill instructs Cascade to read the persona file from `.windsurf/personas/<name>.md` and adopt it for the duration of the skill.

This load is mandatory and not skippable.

### 2. User-driven via `@Personas:<name>` mention

The user may activate a persona at any time by typing in chat:

```
@Personas:<persona-name>
```

Examples:

- `@Personas:investigator` — adopt the investigator persona for the rest of the task
- `@Personas:reviewer` — switch to reviewer mindset
- `@Personas:architect` — bring architect thinking

When Cascade sees `@Personas:<name>` in user input, it MUST:

1. Read `.windsurf/personas/<name>.md` end-to-end.
2. **Re-read it even if the file appears to already be in context** — personas are durable mindsets, and a fresh read guards against context compaction or paraphrase drift.
3. Internalize the persona's identity, principles, communication style, and heuristics.
4. Acknowledge: `🎭 Persona loaded: <name>`.
5. Apply the persona to subsequent reasoning and actions until a different persona is loaded or the task ends.

If `<name>` does not match a file in `.windsurf/personas/`, list the available personas and ask the user to choose.

## Available personas

Available personas are the markdown files in `.windsurf/personas/`. Common ones:

- `@Personas:investigator` — deep investigation, no assumptions
- `@Personas:architect` — system design, trade-offs
- `@Personas:reviewer` — code review, severity discipline
- `@Personas:planner` — phase decomposition, dependency ordering
- `@Personas:spec-author` — evidence-grounded spec authoring
- `@Personas:implementer` — disciplined plan-to-code execution
- `@Personas:verifier` — verification rigor, severity-prioritized fixes
- `@Personas:pattern-matcher` — match existing patterns, don't invent
- `@Personas:communicator` — stakeholder writing, plain-English, 15-year-old test

> **Maintenance:** Keep this list in sync with the actual files in `.windsurf/personas/`. When adding or removing a persona, update both this bullet list and any skills that load it. _Last reviewed: 2026-04-29._

## Multiple personas

A skill or user may need more than one persona. Load them sequentially. The most-recently-loaded persona is the **primary** lens; earlier personas remain available as supporting perspectives.

## Why this convention exists

Cascade's native `@-mention` parser does NOT have a `Personas:` namespace. The `@Personas:<name>` syntax is a **convention** enforced by this rule, not by platform tooling. The agent (Cascade) reads it as an explicit instruction and acts on it. The convention is chosen so personas are visually distinct from file mentions, rule mentions, or skill mentions.
