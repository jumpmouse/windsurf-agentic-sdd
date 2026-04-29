# Architecture Spec (Summary + Tech Plan) — {Topic Title}

> **What this document is**
>
> The **high-level technical design**. Answers "what's the shape of the solution,
> what are the components, what owns what, and what are the tradeoffs?".
>
> Audience: implementer + reviewer. Written after Codebase Investigation is
> complete and before Implementation Spec.
>
> Keep conceptual — no line-by-line code. That is the Implementation Spec.
>
> Delete this instruction block from the final document.

---

> **References:**
> - [Epic Brief](./Epic_Brief_—_{...}.md)
> - [Codebase Investigation](./Codebase_Investigation_—_{...}.md)

---

## 1. Design Summary

### 1.1 Proposed Architecture

{1–3 paragraphs. The shape of the solution at a component/module level. Use
prose first; add the diagram after.}

```mermaid
flowchart TD
  {component A} --> {component B}
  {component B} --> {component C}
```

### 1.2 Key Design Decisions

Each decision is documented with rationale and tradeoffs.

#### Decision 1: {decision name}

**Decision:** {1-sentence statement of what we're doing}

**Rationale:** {why this approach — 2–4 sentences}

**Tradeoffs considered:**

| Option | Pros | Cons | Chosen? |
|--------|------|------|:-------:|
| Option A | ... | ... | ✅ / ❌ |
| Option B | ... | ... | ✅ / ❌ |

**Risks / mitigations:** {what could go wrong, how we mitigate}

#### Decision 2: {...}

{repeat}

### 1.3 Ownership Model

After this epic lands, who owns what?

| Layer | Owns |
|-------|------|
| {Shared layer} | {responsibilities} |
| {Feature A} | {responsibilities} |
| {Feature B} | {responsibilities} |

### 1.4 Change Propagation

What happens when future requirements change?

| Change | Impact |
|--------|--------|
| {hypothetical future change 1} | {affected files / areas} |
| {hypothetical future change 2} | {...} |

---

## 2. Data Model

### 2.1 Data Contracts (Unchanged)

{List existing contracts that this epic does NOT change.}

- **{Interface / DTO / signal name}**: {description; link to `file:` source of
  truth}

### 2.2 New Data Contracts

{List new interfaces / state / DTOs introduced by this epic.}

| Contract | Type | Owner | Purpose |
|----------|------|-------|---------|
| `{Name}` | interface / signal / DTO | {file:...} | {what it carries} |

### 2.3 Backend / Persistence

- **DB schema changes:** {yes/no; if yes, describe + link migration}
- **API contract changes:** {yes/no; if yes, list endpoints}

---

## 3. Component Boundaries

For each new/refactored component, specify:

### 3.1 `{ComponentName}` — `file:{path}`

- **Responsibility:** {one sentence}
- **Inputs:** {list}
- **Outputs:** {list}
- **State owned:** {list}
- **Does NOT own:** {explicit non-responsibilities}

---

## 4. Integration Points

### 4.1 With Existing Code

- {existing component / service} — {how it connects to the new layer}

### 4.2 With External Systems

- {API / library / browser platform} — {how and why}

---

## 5. Constraints & Edge Cases

- **{constraint 1}** — {how the design handles it}
- **{edge case 1}** — {handling}
- **{platform / browser quirk}** — {handling / fallback}

---

## 6. Out-of-Scope (Explicit)

- Not {excluded concern} — {rationale}
- Not {...}

---

## 7. Open Questions

{Any questions that need resolution before the Implementation Spec can be
finalized. Leave empty if none.}

- {question}

---

## Links

- [Epic Brief](./Epic_Brief_—_{...}.md)
- [Codebase Investigation](./Codebase_Investigation_—_{...}.md)
- [Core Flows](./Core_Flows_—_{...}.md)
- [Implementation Spec](./Implementation_Spec_—_{...}.md)
- [Execution Plan](./Execution_Plan.md)
