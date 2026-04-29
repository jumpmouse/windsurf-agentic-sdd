# {TICKET_ID}: {Ticket Title}

> **How to use this template**
>
> A ticket is the **executable unit of work**. It is derived from an Execution Plan
> spec (see `.windsurf/_templates/execution-plan-template.md`) and must be detailed
> enough that an implementer can execute it without further investigation.
>
> **Required level of detail: OPTION 1 — precise references for every code change.**
> Every code-change step must name:
> - The exact file path (use `file:relative/path/from/repo/root` markers)
> - The exact selector / function / line range being changed
> - The current code (Before) and the target code (After), or an explicit transformation rule
> - The rationale (why this change)
>
> Avoid vague phrasing like "update the service" or "fix the styling". Be surgical.
>
> Delete this instruction block from the final ticket.

---

## Scope & Objective

{2–4 sentence description of what this ticket delivers and why it matters in the topic.
Clearly frame the boundary — this ticket covers X, not Y.}

**In scope:**

- {atomic unit of work, 1 line each}
- {atomic unit of work}
- {atomic unit of work}

**Out of scope:**

- {excluded concern} — handled by {OTHER_TICKET_ID or "future topic"}
- {excluded concern}

---

## References

- {Link to parent spec, e.g. `spec:...` external ref or relative path to
  `specs/Execution_Plan.md` that produced this ticket}
- {Link to `specs/Implementation_Spec_—_*.md` section that maps this ticket's files}
- {Link to `specs/Architecture_Spec_—_*.md` decision that drives this ticket, if any}
- **Depends on:** {TICKET_ID(s) that must be complete before this one starts; "none" if independent}
- **Blocks:** {TICKET_ID(s) that cannot start until this is done; omit if none}
- **Risk level:** {🟢 Low | 🟡 Medium | 🔴 High} — {one-sentence reason}

---

## Pre-Implementation Context

{Optional but recommended for Medium/High risk tickets. Capture any facts the
implementer needs that are NOT obvious from reading the files:
- surprising current behavior
- known traps (e.g., "this file also exists in commented blocks — sweep those too")
- historical reason for the current shape
- external validation (e.g., "spec count of 52 was verified against actual repo")}

---

## Detailed Steps

Each step MUST have:
- a numbered heading
- **files touched** (explicit file paths)
- **exact change** (code block or explicit transformation rule with inputs/outputs)
- **rationale** (why this change, tying back to the Scope & Objective)

### 1. {Short verb-first step title — e.g. "Install dependency"}

**Files touched:**

- `file:{dependency-manifest}` (e.g. `package.json`, `pyproject.toml`, `Cargo.toml`)
- `file:{lockfile}`

**Change:**

```
{exact dependency-manager command, e.g. `npm install <pkg>@<version>`,
`pip install <pkg>==<version>`, etc.}
```

**Rationale:** {one sentence — why this version, why now}

---

### 2. {Short verb-first step title — e.g. "Swap a config-file entry"}

**Files touched:**

- `file:{config-file-path}`

**Before** ({path/key inside the file}):

```{language}
{exact existing snippet}
```

**After:**

```{language}
{exact target snippet}
```

**Rationale:** {one sentence — why the new value; if a legacy resource is
left on disk on purpose, name the cleanup ticket: {CLEANUP_TICKET_ID}}

---

### 3. {Repo-wide search/replace step with hotspot references}

**Files touched:** repo-wide `*.{ext1}` + `*.{ext2}` (sweep-driven, not
count-driven).

**Known hotspot files** (NOT exhaustive — execute a repo-wide sweep):

| File | Approx. occurrences |
|------|--------------------:|
| `file:{path/to/hotspot1}` | ~{N} |
| `file:{path/to/hotspot2}` | ~{N} |

**Transformation rules** (apply in order; each must be idempotent):

| From | To | Notes |
|------|----|-------|
| `{regex_from}` | `{replacement}` | {what / why} |
| `{regex_from}` | `{replacement}` | {what / why} |

**Sweep locations that are easy to miss — MUST be included:**

- {comment blocks, e.g. `<!-- ... -->` or `/* ... */`}
- {framework-specific bindings, e.g. `[class]="..."`}
- {string literals in code files}
- {duplicated template blocks (e.g. modal open/close variants)}

**Do NOT touch these false-positive look-alikes:**

- {project-owned identifiers that look like the regex but are intentional}

**Rationale:** {why this sweep is correct; why the false positives are
project-owned}

---

### 4. {Shim step — when direct migration would enlarge blast radius}

**Files touched:**

- `file:{global-shim-file}`
- `file:docs/{TOPIC_SLUG}/{shim-inventory}.md` (inventory entry)
- {list of consumer files that will rely on the shim}

**Shim rule (add to {global-styles | global-init}):**

```{language}
// =====================================================================
// {SHIM-MARKER-TOKEN} — Temporary compatibility shim ({TICKET_ID})
//
// {1-paragraph rationale: why shim instead of direct migration}
//
// Inventory: docs/{TOPIC_SLUG}/{shim-inventory}.md
// Cleanup contract: {CLEANUP_TICKET_ID}
// =====================================================================
{shim definition}
// END {SHIM-MARKER-TOKEN}
```

**Inventory entry to append to `{shim-inventory}.md`:**

```markdown
### SHIM-NN — {short identifier}

- **File:** `{file path}` (search `{SHIM-MARKER-TOKEN}`)
- **Affected selector / API / markup:** {what}
- **Affected consumers:** {list files + occurrence counts}
- **Why the shim exists:** {1–3 sentences}
- **Behavior preserved:** {what default is restored}
- **Cleanup action:** {exact steps for {CLEANUP_TICKET_ID}}
- **Status:** open
```

**Rationale:** {1 sentence on why direct migration would be too big; the
explicit marker + inventory makes the technical debt visible and reviewable.}

---

{Repeat numbered steps for each atomic unit of work. Keep each step implementable
in one coding pass.}

---

## Guardrails

Explicit rules about what MUST NOT change in this ticket. Each guardrail must be
verifiable.

- {Explicit file or behavior that stays unchanged — "Do not rename `data-toggle`
  attributes; that is {OTHER_TICKET_ID} scope."}
- {Performance / correctness invariant — "Bundle size must not exceed current
  baseline by more than X KB."}
- {Cross-cutting concern — "No SSR guards added here; that is {OTHER_TICKET_ID}."}
- {Pre-existing warnings preserved vs. introduced — "Existing warnings carried
  from baseline are acceptable; introducing new warnings is not."}

---

## Acceptance Criteria

A numbered list of conditions that MUST be true when the ticket is done. Each
must be independently verifiable by a grep, a build, or a test.

1. {Binary true/false check — e.g. "Repo-wide sweep: zero occurrences of
   {pattern} remain outside the approved exception list (verified via
   {tool/regex with word-boundary anchors})."}
2. {Binary check — e.g. "{config-file} `{key}` points to `{expected-value}`."}
3. {Binary check — e.g. "Every `{SHIM-MARKER-TOKEN}` in source has a matching
   `### SHIM-NN` entry in `{shim-inventory}.md`."}
4. {Build check — e.g. "`{BUILD_COMMAND}` exits 0."}
5. {Test check — e.g. "`{TEST_COMMAND}` passes at baseline ({N} passed,
   {M} skipped)."}

---

## Verification Steps

Concrete commands + expected results the verifier will run. Each command must be
copy-pastable.

1. **Build verification**
   ```bash
   {BUILD_COMMAND}
   ```
   **Expected:** exit 0, no compilation errors, only pre-existing warnings.

2. **Test verification**
   ```bash
   {TEST_COMMAND}
   ```
   **Expected:** `{baseline pass/fail/skip count}`.

3. **Residue sweep — {category 1}**
   ```bash
   {project-appropriate sweep command, e.g.:
    find {src-root} \( -name '*.{ext1}' -o -name '*.{ext2}' \) \
      -exec {grep|perl|rg} '{regex}' {} \;}
   ```
   **Expected:** zero lines.

4. **Inventory integrity** (only if the ticket introduces shims)
   ```bash
   diff <({find-tool} '{SHIM-MARKER-TOKEN}' {src-root} | sort -u) \
        <(grep -nE '^### SHIM-' docs/{TOPIC_SLUG}/{shim-inventory}.md)
   ```
   **Expected:** 1:1 mapping between source markers and inventory entries.

5. **Guardrail verification**
   - {exact command per guardrail above}

---

## Notes

{Optional: open questions, carry-over technical debt, risks that should be made
visible to the next ticket. Keep concise.}

---

## Filled Example (for reference)

A fully-filled real-world ticket (Bootstrap 4→5 migration in an Angular project)
is preserved at:

- `.windsurf/_templates/examples/ticket__sxe-bootstrap-migration.md`

Use it as a sanity check for what each placeholder slot resolves to in practice.
Do NOT copy its tech-stack-specific content into your own ticket.
