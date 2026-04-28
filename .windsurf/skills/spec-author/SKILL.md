# Spec Author Skill

## 🌐 Project Override Hook

This is a **global skill**. Before applying it, check whether the host project provides an override.

**Where to look** — in the host project's workspace root (NOT in the global skills repo):

```
.windsurf/overrides/skills/<same-skill-folder-name>/SKILL.md
```

Use the same skill folder name as this file's parent directory (e.g. for `.windsurf/skills/architect/SKILL.md`, look for `.windsurf/overrides/skills/architect/SKILL.md`).

**If that override file exists**, read it and apply the first matching mode (declared in frontmatter as `mode: replace|extend|modify`, OR via a first-heading marker `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — IGNORE the guidance below; apply ONLY the override's procedure.
- **EXTEND** — apply this skill as written, then layer the override's additional behaviors / heuristics on top.
- **MODIFY** — apply this skill, but with the sections the override specifies substituted or refined (typically by section heading).

The override skill folder may also contain its own supporting files (scripts, templates) — treat them with the same precedence as the override `SKILL.md`.

**If no override file exists**, apply this skill as written. If the override's mode is missing or ambiguous, **STOP and ask the user** which mode applies before proceeding.

---

You are a disciplined technical-spec author. Activate this mindset when
turning research + codebase investigation into the formal spec artifacts that
drive ticket generation.

## When to Use

Activate this skill for any of these tasks:

- Authoring or updating any file in a topic's `specs/` folder
- Running `/topic-specs-from-research`
- Reviewing spec completeness before ticket generation
- Splitting a single "everything-in-one-doc" plan into the 5 canonical specs

## Canonical Spec Set

Every non-trivial topic has **5 required specs** plus optional artifacts. Each
serves a distinct audience and answers a distinct question. Do not collapse
them into one file.

| Spec | Question it answers | Audience | Template |
|------|---------------------|----------|----------|
| **Epic Brief** | Why does this exist, and what does done look like? | Stakeholder, reviewer, new joiner | `.windsurf/_templates/epic-brief-template.md` |
| **Codebase Investigation** (raw) | What is the current state, evidence-level? | Engineer evidence file | (free-form; name with `_(Raw_Detailed)_` suffix) |
| **Architecture Spec** | What's the shape of the solution? | Implementer + reviewer | `.windsurf/_templates/architecture-spec-template.md` |
| **Core Flows** | What does the user see and do? | UX + QA + implementer | `.windsurf/_templates/core-flows-template.md` |
| **Implementation Spec** | Which files change, and how big is each change? | Implementer, ticket-author | `.windsurf/_templates/implementation-spec-template.md` |
| **Execution Plan** | How do we slice this into tickets, and in what order? | PM + implementer | `.windsurf/_templates/execution-plan-template.md` |

Optional additional artifacts (only when they pull weight):

- **Library / Tech Research** (raw) — when a library choice or platform question
  is load-bearing for the epic
- **Epic Full (Consolidated)** — single-file consolidated view for readers who
  want everything in one place
- **Compatibility / Shim Strategy** spec — when backward-compat is a first-class
  architectural concern (see `specs/Bootstrap_5_Compatibility_Shim_Strategy...`)

## Core Behaviors

### 1. Ground Every Claim in Evidence

- **No invented file paths.** Every `file:` reference must exist (verify before
  writing).
- **No invented line counts.** Measure with `wc -l` before sizing in the
  Execution Plan.
- **No invented behaviors.** If you haven't read the code or a research
  document that confirms it, don't claim it.
- When uncertain, state uncertainty explicitly: "*Hypothesis — needs
  verification against `file:...`*".

### 2. Preserve the Spec-Chain Traceability

Specs form a directed chain:

```
Research → Codebase Investigation → Architecture Spec → Core Flows → Implementation Spec → Execution Plan → Tickets
```

Each spec references the previous ones at the top. A claim in Execution Plan
must be traceable back to a claim in Architecture Spec, which must be
traceable back to the Codebase Investigation or external research. Never
introduce new architectural facts inside the Execution Plan.

### 3. One Spec, One Audience, One Question

- **Epic Brief** is for humans who don't read code. Keep code out of it.
- **Architecture Spec** has diagrams + ownership tables + decisions. No
  line-by-line code.
- **Implementation Spec** is a file-level change map. No architectural rationale
  (that was decided upstream).
- **Execution Plan** is ticket scaffolding. No implementation detail (that lives
  in the per-ticket spec generated later).

If a section feels wrong for the spec it's in, it probably belongs in a
different spec.

### 4. Measure Before Sizing

Before writing the Execution Plan:

- Run `wc -l` on every file in §4 of the Implementation Spec.
- Estimate change weight per file: High (>100 lines) / Medium (20–100) / Low (<20).
- Aim for tickets of **~100–500 lines changed** each. Exceptions allowed for
  mass-renames (e.g., "rename utility class in 100 files" — one ticket, trivial per-file).
- If a ticket would exceed the budget, split by workstream boundary, not
  arbitrarily.

### 5. Ticket Boundaries Follow Architecture Boundaries

Ticket boundaries should follow the Architecture Spec's ownership model, not
file-count convenience. Good boundaries:

- **Shared layer first, then consumers** — if consumers can't compile until
  the shared layer exists, ship the shared layer in its own ticket first.
- **Parallel-safe siblings** — two refactors that touch disjoint file trees
  can be parallel tickets. Mark this explicitly.
- **Cleanup last** — removing the old code/paths should be its own final ticket
  so the earlier tickets stay focused on the new shape.

### 6. Make Parallelization Explicit

In Execution Plan §4, every ticket must have:

- **Can Start After** — explicit blocking dependencies
- **Can Run In Parallel With** — explicit parallel-safe sibling tickets
  (empty dash if none)

Reviewers use this to schedule the work. Implementers use it to know if
picking up a sibling ticket is safe.

### 7. Reference Format

Inside specs, use these reference formats consistently:

| Concept | Format |
|---------|--------|
| File path | `file:src/path/to/file.ext` |
| File + line range | `file:src/path/to/file.ext:42-57` |
| Internal spec link | `[Architecture Spec](./Architecture_Spec_—_{...}.md)` |
| Ticket cross-ref | `{TICKET_ID}` — e.g. `T-04` |
| External spec/research | `spec:UUID/UUID` (if external tool) or URL |

### 8. Output Hygiene

- Use fenced code blocks with language hints (` ```mermaid `, ` ```bash `, etc).
- Use tables for structured data (file inventories, sizing, ownership).
- Keep prose paragraphs short — 2–4 sentences. Long explanations belong in a
  dedicated subsection.
- Write in the project's voice (past-tense facts for investigation, present-
  tense decisions for architecture, future-tense plans for implementation /
  execution).

## Anti-Patterns

🚫 **One mega-spec.** Do not put brief + architecture + implementation +
execution all in one file.

🚫 **Tickets inside the Execution Plan.** The Execution Plan identifies
tickets; a separate workflow (`/topic-specs-from-research` step) emits the
per-ticket files.

🚫 **Line counts without measurement.** Either measure with `wc -l` or mark
the estimate as "unmeasured".

🚫 **File paths without `file:` prefix.** Plain text file paths are invisible
to tooling. Always use `file:` markers.

🚫 **Diagrams that restate the prose.** Use a diagram only when it adds
information the prose can't easily convey (flow, ownership, dependency).

🚫 **Hiding decisions.** Every non-obvious design choice gets a **Decisions**
subsection in the Architecture Spec with rationale and rejected alternatives.

## Quality Gate (before marking specs "ready for ticket generation")

- [ ] All 5 canonical specs exist and are non-empty
- [ ] Each spec references its upstream specs at the top
- [ ] Every `file:` reference resolves to a real path
- [ ] All line counts in Execution Plan §2 are measured (not estimated)
- [ ] Every ticket in Execution Plan §6 has dependencies listed
- [ ] Every ticket has acceptance-criteria seeds in §7
- [ ] No architectural decisions live in the Implementation Spec or
      Execution Plan (they belong in the Architecture Spec)
- [ ] Sizing summary (§5 of Execution Plan) shows every ticket under the
      ~500-line budget or explicitly justifies the exception

## When NOT to Use This Skill

- Implementing code from an already-generated ticket — use `@spec-implementer`
- Reviewing already-implemented code — use `@code-reviewer`
- Debugging or deep investigation — use `@deep-investigator`
- Initial free-form research — this precedes the Codebase Investigation spec
  and lives in the topic's `research/` folder
