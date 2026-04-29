---
name: topic-research
description: Interactive deep investigation of a problem space. Two output modes — standalone (chat-only response) and topic-bound (writes research artifacts to docs/{TOPIC_SLUG}/research/). Self-resolves code-answerable gaps before asking the user anything.
---

# Topic Research Skill

## Persona

**MANDATORY first action:** Load the Investigator persona by reading `.windsurf/personas/investigator.md`. Adopt it for the duration of this skill. If pattern-matching language appears in user input ("exactly like X", "same as Y"), also load `.windsurf/personas/pattern-matcher.md`.

## Inputs

- **PROBLEM** (required) — free-form problem / question statement.
- **TOPIC_SLUG** (optional) — folder name in `docs/`. Provided → topic-bound mode (writes artifacts). Absent → standalone mode (chat output).

## Output modes

| Mode | Trigger | Output |
|------|---------|--------|
| **Standalone** | No `TOPIC_SLUG` | Chat thread only — no files written. |
| **Topic-bound** | `TOPIC_SLUG` provided | One research artifact per area in `docs/{TOPIC_SLUG}/research/` using `.windsurf/_templates/research-artifact-template.md`. Appendix A of that template is the canonical area catalog. |

## Procedure

### 1. Resolve mode + read initial context

Determine output mode from inputs. If `PROBLEM` is missing → STOP and ask the user.

Read list (both modes):
- The PROBLEM statement (extract: scope, success criteria, hard constraints).
- Broad codebase scan: locate likely files via `find` / `grep` based on PROBLEM keywords; read headers; build a rough map.

Additional reads in **topic-bound mode**:
- `docs/{TOPIC_SLUG}/README.md` — topic hub.
- `docs/{TOPIC_SLUG}/CHANGELOG.md` — prior history.
- `docs/{TOPIC_SLUG}/research/` — pre-existing artifacts (avoid duplicates).

Extract a flat list of: known facts, apparent constraints, files / regions in scope, initial questions (raw — will be filtered).

### 2. Investigate from code

For each in-scope file/region:

1. Read it. Confirm what's actually there vs. assumed.
2. Map call sites — for any function / component / config key that may change, find ALL callers and consumers.
3. Trace dependencies — what imports the modules in scope? What do they import?
4. Check for similar patterns — does the codebase handle similar things consistently? Inconsistency is itself a finding.
5. Check tests — what coverage exists? Any tests that pin current behavior?

Track findings as you go — file paths + line ranges for every claim.

### 3. Identify gaps + self-resolve code-answerable ones

Categorize each gap:

| Category | Resolution |
|----------|------------|
| **Code-answerable** | Investigate further: re-grep, read more of the file, check `package.json` / lockfile, configs, git history, tests. |
| **Doc-answerable** | Read the relevant doc / changelog / commit. |
| **User-answerable** | Park for Step 4. |

Per the Investigator persona's "No Stupid Questions" rule, resolve every code-answerable and doc-answerable gap **yourself** before Step 4. If the user-answerable column is empty, skip directly to Step 5.

### 4. Interactive Q&A — user-answerable gaps only

For each remaining user-answerable gap, formulate a question that satisfies "No Stupid Questions":

- **The question** — concrete, scoped, single-issue.
- **What was tried** — your Step 3 evidence that this is genuinely not self-resolvable.
- **Options** — when 2-3 equally valid answers exist in code, present them with trade-offs.

Ask **3-5 questions per round, max**. Use `ask_user_question` when 2-4 discrete options exist; otherwise free-form.

After answers: record verbatim with date. If an answer exposes a new gap, return to Step 2/3 to investigate, then loop. After 2 rounds keep producing new questions → STOP, scope may be larger than this research can close.

### 5. Synthesize findings

Internal consolidated summary:

- **Context + scope** — what was investigated and why.
- **Current-state facts** — concrete, file-cited findings.
- **Blast radius** — call sites, consumers, side effects.
- **Edge cases / risks** — known traps, surprising behaviors, mitigations.
- **Open architectural choices** — for downstream spec authors.
- **Verbatim Q&A** — what was asked, answered, when.

Every claim cites file paths + line ranges. No opinions presented as facts.

### 6. Output — mode-dependent

**Standalone:** emit synthesis directly in chat, sectioned as Step 5. Concise but complete. No files written.

**Topic-bound:** create `docs/{TOPIC_SLUG}/research/`. Produce **one file per research area** using `.windsurf/_templates/research-artifact-template.md`. The template's Appendix A is the canonical catalog (which areas; naming convention; quality bar). Read it before writing the first artifact.

## Quality Gate

- [ ] Every in-scope file in the read list was actually read.
- [ ] Every call-site / dependency claim is grep-verified.
- [ ] No question was asked of the user that could have been self-resolved.
- [ ] All user answers from Step 4 are captured verbatim with dates.
- (Topic-bound) [ ] At least one research artifact exists.
- (Topic-bound) [ ] Each artifact has a non-empty Evidence Index.
- (Topic-bound) [ ] Artifacts cover enough ground for the spec-authoring chain to proceed without additional assumptions.

If any gate fails, fix and re-check before reporting success.

## Final Report

**Standalone:**
```
✅ Research Complete — Standalone

🔍 Findings: {count} files investigated • {count} code-answerable gaps resolved • {count} Q&A rounds
(Findings presented in-thread above.)
```

**Topic-bound:**
```
✅ Research Complete — {TOPIC_SLUG}

📁 Artifacts: docs/{TOPIC_SLUG}/research/01-*.md, 02-*.md, ...

❓ Unresolved (parked for downstream): {list}

🎯 Next: /topic-specs-from-research
```

Then **STOP**. Do not auto-route — user reviews artifacts first.
