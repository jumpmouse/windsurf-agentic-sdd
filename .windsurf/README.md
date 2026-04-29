# Windsurf Agentic-SDD System — How It Works

A Windsurf-native, **spec-driven development (SDD)** system. It separates **what to build** (specs), **how to slice it** (Execution Plan), and **what to do next** (tickets), and threads everything through a clean, sessionable workflow chain. Built around four primitives:

- **Workflows** — thin slash commands the user invokes (`/kickoff`, `/topic-research`, `/topic-implement`, …).
- **Skills** — multi-step procedures with templates and supporting files. Where the heavy lifting lives.
- **Personas** — reusable, mindset-focused profiles (the system's analog to Claude Code "subagents"). Loaded by skills (or by the user via `@Personas:<name>`).
- **Rules** — global behavioral constraints. Always-on rules are injected into every Cascade message; behavioral rules govern conduct.

This README is the single source of truth for how the parts fit together.

---

## TL;DR — the three things to know

1. **Workflows are thin shells.** Each one loads a skill and gets out of the way.
2. **Skills load personas.** Every skill begins with `MANDATORY: read .windsurf/personas/<name>.md`. The persona is the *mindset*; the skill is the *recipe*.
3. **The user is in charge.** Workflows STOP at user-review gates between steps. The system never auto-chains past a gate.

---

## Folder structure

```
.windsurf/
├── README.md                    ← this file
├── rules/                       ← always-on + behavioral
│   ├── 00-execution-protocol.md      (always_on) strict step-by-step protocol
│   ├── 01-override-hook.md           (always_on) host-project override mechanism
│   ├── 02-personas-system.md         (always_on) the @Personas:<name> convention
│   ├── communication-during-work.md  (always_on) explain-before-changing discipline
│   └── fixing-bugs.md                (always_on) bug-fix discipline + 3-attempt rule
├── personas/                    ← mindsets (Claude Code subagent style, adapted)
│   ├── investigator.md
│   ├── architect.md
│   ├── reviewer.md
│   ├── implementer.md
│   ├── planner.md
│   ├── spec-author.md
│   ├── verifier.md
│   ├── pattern-matcher.md
│   └── communicator.md
├── skills/                      ← procedural; each folder has SKILL.md + (optional) supporting files
│   ├── topic-research/
│   ├── topic-specs-authoring/
│   ├── topic-spec-planning/
│   ├── topic-tickets-authoring/
│   ├── topic-tickets-verification/
│   ├── topic-implementation/
│   ├── topic-verification/
│   ├── topic-closure/
│   ├── session-logging/
│   ├── stakeholder-reporting/
│   ├── plantuml/
│   └── generate-local-setup/
├── workflows/                   ← thin slash-command entry points
│   ├── kickoff.md
│   ├── topic-new.md
│   ├── topic-research.md
│   ├── topic-specs-from-research.md
│   ├── topic-spec-plan.md
│   ├── topic-tickets-from-plan.md
│   ├── topic-tickets-verify.md
│   ├── topic-implement.md
│   ├── topic-verify.md
│   ├── topic-close.md
│   ├── log-session.md
│   ├── stakeholder-report.md
│   ├── generate-local-setup.md
│   └── init-docs.md
└── _templates/                  ← shared content scaffolds (5 spec templates, ticket, reports, etc.)
    ├── architecture-spec-template.md
    ├── core-flows-template.md
    ├── epic-brief-template.md
    ├── execution-plan-template.md
    ├── implementation-spec-template.md
    ├── research-artifact-template.md
    ├── ticket-template.md
    ├── ticket-report-template.md
    ├── verification-report-template.md
    ├── session-changes-template.md
    ├── work-log-entry-template.md
    ├── stakeholder-report-template.md
    ├── daily-report-template.md
    ├── changelog-template.md
    └── examples/                ← real-world worked examples
```

---

## The flow — from problem to commit

This is what a typical end-to-end run looks like for a **new piece of work**:

```
/kickoff                                  status check + suggestion
/topic-new                                create docs/{TOPIC_SLUG}/ folder structure
/topic-research                           interactive Q&A → research artifacts (Investigator)
/topic-specs-from-research                author 5 conceptual specs (Spec Author)
                                          ⏸ user review gate — review specs
/topic-spec-plan                          author Execution Plan (Spec Author + Planner)
                                          ⏸ user review gate — review plan
/topic-tickets-from-plan                  generate per-ticket files (Spec Author)
                                          ⏸ user review gate — review tickets
/topic-tickets-verify                     verify tickets ↔ specs (Verifier)
                                          ⏸ user review gate — fix or proceed
/kickoff {first_ticket_id}                pick the first ticket
/topic-implement {TICKET_ID}              build it (Implementer)
                                          ⏸ user review gate — review code
/topic-verify {TICKET_ID}                 verify implementation (Verifier)
                                          ⏸ user review gate — review verification report
/topic-close {TICKET_ID}                  commit + reports + session log (Reviewer)
                                          ⏸ commit-plan gate before commits land
```

**Each ⏸ is a hard STOP.** The workflows don't chain past a user-review gate. You decide what to do next.

For an **existing topic**, you typically skip everything before the active ticket and resume with `/kickoff {TICKET_ID}` (status check) → `/topic-implement` / `/topic-verify` / `/topic-close` depending on what the ticket needs.

---

## The four primitives, explained

### 1. Rules — the always-on stuff

Rules in `.windsurf/rules/` are **always_on**, meaning their content is injected into every Cascade message in this workspace. They define behavior that should never be forgotten:

| Rule | What it does |
|------|--------------|
| `00-execution-protocol.md` | Strict step-by-step workflow execution. No skipping, no combining, STOP at the end. |
| `01-override-hook.md` | How a host project can override system files (REPLACE / EXTEND / MODIFY) without forking. |
| `02-personas-system.md` | The `@Personas:<name>` convention. Tells Cascade how to load a persona on demand. |
| `communication-during-work.md` | Explain-before-changing discipline. |
| `fixing-bugs.md` | Bug-fix scope + 3-attempt rule. |

Rules are short on purpose. The 3 always-on system rules are the closest thing to a "boot script" for the agent — every conversation starts with them in context.

### 2. Personas — the mindsets

Personas live in `.windsurf/personas/`. Each one is a small markdown file with:

- **Identity** — who this persona is, in one sentence.
- **Background & expertise** — what they bring to the task.
- **Core principles** — the rules they live by.
- **Communication style** — how they speak.
- **Heuristics** — rules of thumb for their domain.
- **When NOT to use** — pointers to better-fit personas.

There are **9 personas**:

| Persona | When to load |
|---------|--------------|
| `investigator` | Deep code investigation, no assumptions, end-to-end tracing. |
| `architect` | System design, trade-offs, ADRs. |
| `reviewer` | Pre-merge review, severity discipline, side-effect awareness. |
| `implementer` | Disciplined plan-to-code execution, three-tier boundaries. |
| `planner` | Phase decomposition, dependency ordering, sizing discipline. |
| `spec-author` | Evidence-grounded spec writing, traceability, audience-by-spec. |
| `verifier` | Severity-prioritized fixes, atomic, regression-aware. |
| `pattern-matcher` | When user says "exactly like X" — match, don't invent. |
| `communicator` | Stakeholder writing, 15-year-old test, no jargon. |

Personas are **shareable**. A skill can load one or more, and the user can also load one directly via the `@Personas:<name>` convention.

### 3. Skills — the procedures

Skills live in `.windsurf/skills/<skill-name>/SKILL.md`. They are **invoked by workflows** (or by the model when their description matches a request). Each SKILL.md has:

- YAML frontmatter (`name`, `description`).
- A **mandatory** first action: load the persona it requires.
- The full procedure (steps, quality gate, error handling).
- References to templates in `.windsurf/_templates/` and to other skills it can call (e.g. `session-logging`).

Skills are sized to be readable end-to-end — typically 3-7 KB. Heavy supporting content (templates, examples) lives outside the SKILL.md (in `_templates/` or as skill-local files).

There are **12 skills**, mapped to workflows:

| Skill | Loaded by workflow | Persona |
|-------|--------------------|---------|
| `topic-research` | `/topic-research` | `investigator` (+ `pattern-matcher`) |
| `topic-specs-authoring` | `/topic-specs-from-research` | `spec-author` |
| `topic-spec-planning` | `/topic-spec-plan` | `spec-author` + `planner` |
| `topic-tickets-authoring` | `/topic-tickets-from-plan` | `spec-author` |
| `topic-tickets-verification` | `/topic-tickets-verify` | `verifier` |
| `topic-implementation` | `/topic-implement` | `implementer` (+ `pattern-matcher`) |
| `topic-verification` | `/topic-verify` | `verifier` |
| `topic-closure` | `/topic-close` | `reviewer` |
| `session-logging` | `/log-session` | `reviewer` |
| `stakeholder-reporting` | `/stakeholder-report` | `communicator` |
| `generate-local-setup` | `/generate-local-setup` | `investigator` |
| `plantuml` | (no workflow — model-invoked) | `architect` |

### 4. Workflows — the slash commands

Workflows live in `.windsurf/workflows/`. They are **manual-only** — Cascade never auto-invokes them. Each one is a thin shell that:

1. Declares its inputs.
2. Loads its skill via `read_file`.
3. Hands execution to the skill.
4. Stops when the skill stops.

There are **14 workflows**:

| Workflow | What it does |
|----------|--------------|
| `/kickoff` | Status check + next-step suggestion. Bugs Bunny mode if all done. |
| `/topic-new` | Create a new topic folder with standard structure. |
| `/topic-research` | Interactive deep investigation. |
| `/topic-specs-from-research` | Author the 5 conceptual specs. |
| `/topic-spec-plan` | Author the Execution Plan. |
| `/topic-tickets-from-plan` | Generate per-ticket spec files. |
| `/topic-tickets-verify` | Pre-implementation ticket quality gate. |
| `/topic-implement` | Build a ticket. |
| `/topic-verify` | Verify implementation. |
| `/topic-close` | Final review + commits (only workflow that commits). |
| `/log-session` | Reconcile SESSION-CHANGES + CHANGELOG + WORK_LOG. |
| `/stakeholder-report` | Non-technical update for non-engineers. |
| `/generate-local-setup` | Walk project, create root `AGENTS.md`. |
| `/init-docs` | Bootstrap `docs/` folder + `WORK_LOG.md`. |

---

## How to use this system on a new project

```bash
# 1. Generate the project profile (creates AGENTS.md at root)
/generate-local-setup

# 2. Bootstrap docs/ (creates docs/README.md + docs/WORK_LOG.md)
/init-docs

# 3. Create your first topic
/topic-new
# Provide: TOPIC_SLUG, TOPIC_TITLE, TOPIC_DESCRIPTION

# 4. Investigate
/topic-research
# Provide: PROBLEM (and TOPIC_SLUG to write artifacts)

# 5. Author the spec chain (3 sessions, 3 user-review gates)
/topic-specs-from-research
# ⏸ review the 5 specs
/topic-spec-plan
# ⏸ review the Execution Plan
/topic-tickets-from-plan
# ⏸ review the generated tickets

# 6. Verify the tickets
/topic-tickets-verify

# 7. Pick a ticket and start
/kickoff T-01
/topic-implement T-01
# ⏸ review the changes
/topic-verify T-01
# ⏸ review the verification report
/topic-close T-01
# ⏸ approve the commit plan
```

The whole cycle from "new topic" to "first ticket closed" is roughly 6 sessions, each ending at a user-review gate.

---

## Working with personas

### Skills load personas automatically

Every skill begins with a mandatory directive like:

> **MANDATORY first action:** Load the Investigator persona by reading `.windsurf/personas/investigator.md`. Adopt it for the duration of this skill.

Cascade reads the file, internalizes the persona, and applies it to subsequent reasoning.

### You can load a persona directly

If you want to switch mindsets mid-conversation, type:

```
@Personas:investigator
```

Cascade will read `.windsurf/personas/investigator.md` (re-reading even if it was loaded earlier — personas are durable mindsets), and acknowledge with `🎭 Persona loaded: investigator`. Subsequent reasoning will be shaped by that persona until you load a different one.

This is a **convention**, not a Cascade platform feature. The `@Personas:<name>` syntax is recognized because the always-on rule `02-personas-system.md` tells Cascade how to interpret it.

### Multiple personas can coexist

A skill or user can load more than one persona. The most-recently-loaded is the **primary lens**; earlier personas remain available as supporting perspectives. For example, `topic-spec-planning` loads `spec-author` (primary) and `planner` (supporting) — together they make a full planning mindset.

---

## Per-project customization (overrides, no forking)

This system is shared across projects. To customize for a specific host project **without modifying the system files**, drop an override in:

```
.windsurf/overrides/
├── workflows/<same-filename>.md
├── skills/<same-skill-folder>/SKILL.md
├── personas/<same-persona-filename>.md
└── rules/<same-rule-filename>.md
```

Each override declares one of three modes via YAML frontmatter `mode:` or its first heading:

- **REPLACE** — apply only the override.
- **EXTEND** — apply the system file, then layer the override on top.
- **MODIFY** — apply the system file with the sections the override specifies substituted.

The always-on `01-override-hook.md` rule defines this contract globally — individual workflows / skills / personas don't need to repeat the override-check boilerplate.

---

## Doc system convention

Outputs from the SDD chain land under `docs/{TOPIC_SLUG}/`:

```
docs/
├── README.md                       ← topic hub (created by /init-docs)
├── WORK_LOG.md                     ← session-based root log (newest on top)
└── {TOPIC_SLUG}/
    ├── README.md                   ← topic overview, status table, links
    ├── CHANGELOG.md                ← per-topic, date-grouped
    ├── SESSION-CHANGES-{DATE}.md   ← per-session changes (idempotent)
    ├── specs/                      ← 5 conceptual specs + Execution Plan
    ├── tickets/                    ← per-ticket spec files (T-01__*.md, T-02__*.md, …)
    ├── reports/                    ← completion + verification reports
    ├── research/                   ← (conditional) raw research artifacts
    └── stakeholder-reports/        ← (conditional) non-technical updates
```

Convention notes:

- **No separate root CHANGELOG.** The root `WORK_LOG.md` plays that role with the added benefit of being session-grouped.
- **Per-topic CHANGELOG** captures what changed in this topic and when, with ticket + commit refs.
- **SESSION-CHANGES files** are per-day, idempotent, and the dispatcher between in-flight work and committed history.

---

## Files of note

- **`AGENTS.md`** (at project root) — generated by `/generate-local-setup`. The single source of truth for `{BUILD_COMMAND}` / `{TEST_COMMAND}` resolution. Workflows that reference these placeholders read the first fenced code block under `## Build Command` and `## Test Command` headings.

- **`docs/WORK_LOG.md`** — session-based root log. Created by `/init-docs`. Updated by `/log-session` (and indirectly by `/topic-close` and the spec-chain workflows).

- **`docs/{TOPIC_SLUG}/CHANGELOG.md`** — per-topic. Append-only, date-grouped, with ticket + commit refs.

---

## Design notes (why it's shaped this way)

**Workflows are thin** because Windsurf workflows are user-facing manual triggers — they should be readable in seconds. Heavy procedure belongs in skills.

**Skills load personas** because mindset and procedure are different concerns. A persona is portable across skills (the Investigator's "no assumptions" discipline applies whether you're researching, generating local setup, or debugging). A skill is a recipe specific to one job.

**Personas are the analog to Claude Code subagents.** Windsurf does not (yet) expose user-defined subagents the way Claude Code does. Personas fill the same niche — reusable specialist roles — without requiring platform features the host lacks.

**Always-on rules are short and few.** Long always-on content costs context budget on every message. The 3 system always-on rules + 2 behavioral always-on rules total ~3-4 KB combined — affordable.

**The override hook is global, not per-file.** Putting the REPLACE / EXTEND / MODIFY contract in a single always-on rule is dramatically smaller than repeating ~500 chars of the same hook in every workflow / skill / rule.

**STOP at user-review gates** is non-negotiable. The system is intentionally manual at decision points so the user stays in the loop. Auto-chaining past a gate would erode the trust that makes this system work.

---

## Glossary

| Term | Meaning |
|------|---------|
| **Topic** | A self-contained area of work (epic-equivalent), folder under `docs/`. |
| **Ticket** | A unit of work inside a topic, named `T-01`, `T-02`, …, with its own spec file. |
| **Epic Brief** | The "why" + scope spec. Audience: stakeholder. |
| **Codebase Investigation** | Raw current-state evidence file. Audience: engineer onboarding. |
| **Architecture Spec** | Key design decisions + ownership. Audience: implementer + reviewer. |
| **Core Flows** | User-visible interactions. Audience: UX + QA + implementer. |
| **Implementation Spec** | File-level change map. Audience: ticket-author. |
| **Execution Plan** | Ticket inventory + sizing + dependencies + parallelization. Audience: PM + implementer. |
| **Persona** | Reusable mindset profile (this system's analog to Claude Code subagent). |
| **Skill** | Procedural recipe with templates and supporting files (Windsurf primitive). |
| **Workflow** | Thin slash-command entry point (Windsurf primitive, manual-only). |
| **Rule** | Behavioral constraint (Windsurf primitive). Always-on rules are injected every message. |

---

## Related files

- **`.windsurf/rules/00-execution-protocol.md`** — the strict step-by-step contract.
- **`.windsurf/rules/01-override-hook.md`** — the per-project customization mechanism.
- **`.windsurf/rules/02-personas-system.md`** — the `@Personas:<name>` convention.

If something here disagrees with one of those rules, the rule wins — it's globally always-on, and this README is just documentation.
