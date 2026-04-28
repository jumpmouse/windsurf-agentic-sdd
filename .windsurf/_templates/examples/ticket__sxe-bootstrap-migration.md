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
  `specs/Execution_Plan_—_*.md` that produced this ticket}
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

### 1. {Short verb-first step title, e.g. "Install Bootstrap 5 package"}

**Files touched:**

- `file:package.json`
- `file:package-lock.json`

**Change:**

```
npm install bootstrap@^5.2.3 @popperjs/core@^2.11.6
```

**Rationale:** {one sentence}

---

### 2. {Short verb-first step title, e.g. "Swap the build-time CSS entry to Bootstrap 5"}

**Files touched:**

- `file:angular.json`

**Before** (`projects.Mining.architect.build.options.styles`):

```json
"styles": [
  "src/assets/website/css/bootstrap.min.css",
  ...
]
```

**After:**

```json
"styles": [
  "node_modules/bootstrap/dist/css/bootstrap.min.css",
  ...
]
```

**Rationale:** Switch from the vendored Bootstrap 4 build to the fresh npm-installed
Bootstrap 5 build. The legacy asset file stays on disk until {CLEANUP_TICKET_ID} to
keep the diff surgical.

---

### 3. {Template for a repo-wide search/replace step with hotspot references}

**Files touched:** repo-wide `*.html` + `*.scss` (sweep-driven, not count-driven).

**Known hotspot files** (NOT an exhaustive list — execute a repo-wide sweep):

| File | Approx. occurrences |
|------|--------------------:|
| `file:src/.../foo.component.html` | ~12 |
| `file:src/.../bar.component.scss` | ~3 |

**Transformation rules** (apply in order; each must be idempotent):

| From | To | Notes |
|------|----|-------|
| `(?<![-\w])ml-([0-5]|auto)(?![-\w])` | `ms-$1` | directional margin |
| `(?<![-\w])mr-([0-5]|auto)(?![-\w])` | `me-$1` | directional margin |
| `(?<![-\w])ml-(sm\|md\|lg\|xl)-([0-5]|auto)(?![-\w])` | `ms-$1-$2` | responsive variant |

**Sweep locations that are easy to miss — MUST be included:**

- HTML comment blocks (`<!-- ... -->`)
- Angular class bindings: `[className]="..."`, `[ngClass]="{ 'foo': cond }"`
- String literals in `.ts` files
- Duplicated template blocks (e.g., modal open/close variants)

**Do NOT touch these false-positive look-alikes:**

- `.ml-14`, `.mr-275`, `.pl-10`, `.pr-10`, `.pl-mobile-code`, `.pr-mobile-code`
- `.text-right-768`, `.close-button`, `.soma-pl-40`

**Rationale:** Bootstrap 5 drops LTR-assuming directional utilities in favor of
logical-property utilities. The project-owned classes above are custom and must
remain.

---

### 4. {Template for a shim step — when direct migration would enlarge blast radius}

**Files touched:**

- `file:src/styles.scss` (global shim)
- `file:docs/{topic-slug}/bootstrap-shim.md` (inventory entry)
- {list of consumer files that will rely on the shim}

**Shim rule (add to global styles):**

```scss
// =====================================================================
// BOOTSTRAP5-COMPAT — Temporary compatibility shim ({TICKET_ID})
//
// {1-paragraph rationale: why shim instead of direct migration}
//
// Inventory: docs/{topic-slug}/bootstrap-shim.md
// Cleanup contract: {CLEANUP_TICKET_ID}
// =====================================================================
.legacy-bs4-class {
  {BS4 default behavior re-declared here}
}
// END BOOTSTRAP5-COMPAT
```

**Inventory entry to append to `bootstrap-shim.md`:**

```markdown
### SHIM-NN — `.legacy-bs4-class` description

- **File:** `src/styles.scss` (global shim, search `BOOTSTRAP5-COMPAT`)
- **Affected selector / markup:** `.legacy-bs4-class`
- **Affected templates:** {list files + occurrence counts}
- **Why the shim exists:** {1–3 sentences}
- **Behavior preserved:** {what default is restored}
- **Cleanup action:** {exact steps for {CLEANUP_TICKET_ID}}
- **Status:** open
```

**Rationale:** The direct semantic migration of `.legacy-bs4-class` would touch
{N} files and risk breaking {area}. Tracking as an explicit shim with a named
cleanup ticket keeps the blast radius in this ticket small while making the
technical debt visible and reviewable.

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

1. {Check phrased as a binary true/false, e.g. "Repo-wide sweep: zero BS4
   directional utilities remain outside approved custom-class list (verified
   via perl-regex with word boundaries on both sides)."}
2. {Check, e.g. "`angular.json` styles entry points to
   `node_modules/bootstrap/dist/css/bootstrap.min.css`."}
3. {Check, e.g. "Every `BOOTSTRAP5-COMPAT` marker in source has a matching
   `### SHIM-NN` entry in `bootstrap-shim.md`."}
4. {Check, e.g. "`ng build --configuration prod` exits 0."}
5. {Check, e.g. "`ng test` passes at baseline ({N} passed, {M} skipped)."}

---

## Verification Steps

Concrete commands + expected results the verifier will run. Each command must be
copy-pastable.

1. **Build verification**
   ```bash
   npx ng build --configuration prod
   ```
   **Expected:** exit 0, no compilation errors, only pre-existing warnings.

2. **Test verification**
   ```bash
   npx ng test --watch=false --browsers=ChromeHeadlessCustom
   ```
   **Expected:** `TOTAL: {N} SUCCESS, {M} skipped` (baseline match).

3. **Residue sweep — {category 1}**
   ```bash
   find src \( -name '*.html' -o -name '*.scss' \) -exec perl -nlE \
     'while (/(?<![-\w]){BS4_PATTERN}(?![-\w])/g) { say "$ARGV:$.: $&"; }' {} \;
   ```
   **Expected:** zero lines.

4. **Inventory integrity**
   ```bash
   diff <(grep -rln 'BOOTSTRAP5-COMPAT' src | sort -u) \
        <(grep -nE '^### SHIM-' docs/{topic-slug}/bootstrap-shim.md)
   ```
   **Expected:** 1:1 mapping between source markers and inventory entries.

5. **Guardrail verification**
   - {exact command per guardrail above}

---

## Notes

{Optional: open questions, carry-over technical debt, risks that should be made
visible to the next ticket. Keep concise.}
