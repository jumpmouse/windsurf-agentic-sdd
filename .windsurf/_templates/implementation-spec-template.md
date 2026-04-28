# Implementation Spec (Summary) — File-Level Change Map for {Topic Title}

> **What this document is**
>
> The **file-level change map** that turns the Architecture Spec into actionable
> edits. Answers "which files will be created, modified, or deleted, and what is
> the change weight of each?".
>
> Audience: implementer + ticket-author. This document is the **direct input**
> to the Execution Plan (which sizes and sequences the work into tickets).
>
> This is the **bridge** between architecture and ticket breakdown. It does NOT
> yet divide work into tickets — that is the Execution Plan.
>
> Delete this instruction block from the final document.

---

> **References:**
> - [Architecture Spec](./Architecture_Spec_—_{...}.md)
> - [Core Flows](./Core_Flows_—_{...}.md)

---

## 1. Why This Spec Exists

{1–2 paragraphs. What does this document enable? Typically: "turn the validated
architecture into an implementation-oriented map of what gets created,
modified, or removed, and in what order."}

---

## 2. Implementation Summary

{3–6 sentence summary of the whole implementation at a file level. Which layers
change, what gets introduced, what gets retired.}

### Core direction

- {high-level step 1}
- {high-level step 2}
- {high-level step 3}

```mermaid
flowchart TD
  A[Step 1: {...}] --> B[Step 2: {...}]
  A --> C[Step 3 (parallel): {...}]
  B --> D[Step 4: {...}]
  C --> D
  D --> E[Step 5 (verification): {...}]
```

---

## 3. Final Ownership Model After Implementation

| Layer | Owns |
|-------|------|
| {Layer A} | {responsibilities} |
| {Layer B} | {responsibilities} |
| {Layer C} | {responsibilities} |

---

## 4. File-Level Change Map

### 4.1 New Files

| File | Est. Lines | Purpose |
|------|-----------:|---------|
| `file:{path}` | ~{N} | {one-line purpose} |
| `file:{path}` | ~{N} | {...} |

### 4.2 Modified Files

| File | Current Lines | Change Weight | Summary of change |
|------|-------------:|:-------------:|-------------------|
| `file:{path}` | {current} | High / Medium / Low | {what changes and why} |

### 4.3 Removed / Retired Files

| File / Area | Current Lines | Status | Reason |
|-------------|-------------:|:------:|--------|
| `file:{path}` | {current} | Retire / Delete | {why} |

### 4.4 Moved / Renamed Files

| From | To | Reason |
|------|----|--------|
| `file:{old path}` | `file:{new path}` | {why} |

---

## 5. Workstream Breakdown

Workstreams are logical groupings of work that can be implemented as a unit.
One workstream typically maps to one or more tickets.

### Workstream A — {name}

- **Objective:** {1 sentence}
- **Output:** {concrete artifacts / behavior changes}
- **Why first / this order:** {dependency rationale}
- **Files involved:** {subset of §4 above}
- **Approx. line weight:** ~{N}

### Workstream B — {name}

{repeat}

---

## 6. Cross-Cutting Concerns

### 6.1 Migration / Back-Compatibility

- **Breaking changes:** {list, or "none"}
- **Shims / compatibility layers introduced:** {list with cleanup ticket refs}

### 6.2 Tests to Update / Add

- **Existing tests affected:** {list with `file:` refs and what changes}
- **New tests required:** {list}

### 6.3 Docs to Update

- {files outside `specs/` that need updating when implementation lands}

---

## 7. Verification Strategy

How will we confirm the implementation is correct?

- **Build:** {what commands, expected outcome}
- **Unit tests:** {scope, target pass count}
- **Manual QA:** {flows to exercise — link to Core Flows}
- **Regression sweeps:** {grep patterns, visual diff, etc}

---

## 8. Open Questions Surfaced During Implementation Planning

- {question} — {who should answer / when}

---

## Links

- [Epic Brief](./Epic_Brief_—_{...}.md)
- [Architecture Spec](./Architecture_Spec_—_{...}.md)
- [Core Flows](./Core_Flows_—_{...}.md)
- [Execution Plan](./Execution_Plan_—_{...}.md)
