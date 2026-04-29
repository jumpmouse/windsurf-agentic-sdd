# {Research Artifact Title — short, concrete, scoped to one question or area}

> **How to use this template**
>
> A research artifact is the **output of one focused investigation** during
> `/topic-research`. One file per research area (e.g. `01-blast-radius.md`,
> `02-data-flow.md`, `03-edge-cases.md`, `04-third-party-api.md`). The
> file numbers reflect the order findings were produced.
>
> The artifact is **evidence, not opinion**. It is the source of truth that
> `/topic-specs-from-research` consumes when authoring the 5-spec set.
>
> **Required level of rigor:** every claim cites a specific file + line
> range, a specific command output, or an explicit user answer (with date).
> Anything the agent could not verify must be flagged in **Open Questions**
> or **Limitations**.
>
> Delete this instruction block from the final artifact.

---

## 1. Question / Investigation Scope

**One sentence**: what specific question does this artifact answer?

Example shape (do not copy literally):
- *"Which call sites does function `X.bar()` have, and which of them depend
  on its current return-shape?"*
- *"What is the existing data flow between modules `A`, `B`, and the
  external `Z` API?"*
- *"Which of the 12 component files in `feature/foo/` survive the planned
  refactor unchanged, and which need migration?"*

**Why this needs to be settled before specs are written:**
{1–3 sentences — what would go wrong in the spec / Execution Plan if this
question were left ambiguous}

---

## 2. Method

How the investigation was conducted, in order. This must be reproducible —
another agent or human should be able to re-run the same checks.

| # | Action | Tool / Command | Why |
|---|--------|----------------|-----|
| 1 | {action — e.g. "Locate every call site of `bar()`"} | `{exact command, e.g. grep -rn '\\.bar(' src/}` | {1 sentence} |
| 2 | {action} | {tool} | {why} |
| 3 | {action} | {tool} | {why} |

Files / regions read:

- `file:{path}:{line range}` — {what was checked}
- `file:{path}:{line range}` — {what was checked}

External sources consulted (only if applicable):

- {URL or doc reference} — {what was checked}

---

## 3. Findings

The substantive content. Use whatever structure (table, prose, diagram)
best fits the question. Every assertion must cite evidence.

### 3.1 {First finding heading}

{Statement of finding.} **Evidence:** `file:{path}:{line}` shows {what}.

### 3.2 {Second finding heading}

{Statement.} **Evidence:** {output of `wc -l file` was {N} lines, run
{date}}.

### 3.3 {Diagram, if helpful}

```mermaid
%% Optional — only if a flow / dependency graph clarifies the finding
flowchart LR
    A[{node}] --> B[{node}]
```

---

## 4. Evidence Index

A flat list every cited file/region/command, so a reviewer can audit
without re-tracing the prose.

| Citation | Evidence |
|----------|----------|
| §3.1 | `file:{path}:{line range}` |
| §3.1 | command: `{command}` → output excerpt: `{...}` |
| §3.2 | `file:{path}:{line range}` |

---

## 5. Open Questions Resolved (Q&A with user)

Questions that the agent could **not** answer from code/docs alone, which
were answered by the user during `/topic-research` Step 4. Each entry must
justify why the question wasn't self-resolvable.

### Q-1 — {short question slug}

- **Question asked:** {exact question}
- **Why this could not be inferred from code/docs:** {1–3 sentences naming
  what was checked first — e.g. "Searched for any config flag in
  `config/*.json`, environment variables in `.env*`, and feature toggles in
  `src/feature-flags/` — none of them define this preference"}
- **User's answer:** {the user's response, verbatim or summarized}
- **Date answered:** {YYYY-MM-DD}
- **Implication for specs:** {1 sentence — which spec section will encode
  this answer}

### Q-2 — {short question slug}

{same structure}

---

## 6. Open Questions / Limitations (still unresolved)

Questions that remain open after this investigation. Each must say what
would need to happen to close the gap.

- **{Question}** — {what's blocking resolution}: {missing input, external
  decision needed, follow-up investigation needed}.

If there are **none**, write *"None — investigation is complete for this
scope."*

---

## 7. Implications for the 5-Spec Set

How this artifact's findings feed downstream. One bullet per spec that will
be affected.

- **Epic Brief** — {what scope or success-criterion derives from this
  finding; or "no impact"}
- **Codebase Investigation** — {which raw facts get carried over}
- **Architecture Spec** — {which decision or constraint this surfaces}
- **Core Flows** — {which user flow / interaction this affects; or "no
  impact"}
- **Implementation Spec** — {which file-level entries this provides}

---

## 8. Cross-References

- **Related research artifacts:** {`research/0X-...md`} (if findings
  overlap or one depends on another)
- **Ticket spec (if known):** {`tickets/T-NN__*.md`}
- **External research / Traycer artifact:** {ref}

---

## Appendix A — Suggested Area Breakdowns

> This appendix is **for `/topic-research` to consume**, not part of an
> individual artifact. Delete it when adapting this template into a
> specific artifact file.

When `/topic-research` runs in **topic-bound mode**, it produces one
artifact per research area. Use the breakdown below as the canonical
catalog. **Use only the areas that apply** — do NOT pad the folder with
empty files. Numbering reflects the order findings were investigated.

| File | Area | When to include |
|------|------|-----------------|
| `01-context-and-scope.md` | Problem being researched, success criteria | Always |
| `02-current-state.md` | Codebase facts from the investigation step | Always (even if brief) |
| `03-blast-radius.md` | Call sites, consumers, side effects of any change | When a change touches a function / module / config used elsewhere |
| `04-data-flow.md` | Data movement, API shape, transformations | Only if non-trivial — skip for pure-UI or local-state work |
| `05-edge-cases.md` | Known traps, off-by-ones, surprising existing behaviors | When prior code has them; otherwise skip |
| `06-decisions-needed.md` | Open architectural choices the spec authors must close | When research surfaces 2+ valid design options |
| `07-risks.md` | Risks + suggested mitigations | When there are non-trivial risks (data loss, security, perf, regression) |
| `08-qa-with-user.md` | Verbatim Q&A transcript from the interactive Q&A step | When at least one user-answerable gap was closed via Q&A |

### Naming convention

```
docs/{TOPIC_SLUG}/research/{NN}-{kebab-case-area}.md
```

- `{NN}` is the two-digit order in which the area was investigated, NOT a
  fixed identity. If `04-data-flow.md` is skipped, the next file is still
  `05-...`.
- `{kebab-case-area}` is a short slug (≤4 words) describing the
  investigation focus. Re-use the canonical names above when the area
  matches; otherwise pick something concrete (e.g. `04-third-party-api.md`).

### Quality bar per artifact

Each artifact must:

- Cite specific file paths + line ranges as evidence (no opinions)
- Have a non-empty **Evidence Index** (Section 4)
- Have a **Cross-References** section (Section 8) linking related
  artifacts and (if applicable) the ticket spec
- Stand alone — a reader of just one artifact must be able to understand
  its scope without reading the others
