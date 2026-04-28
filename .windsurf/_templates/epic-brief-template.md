# Epic Brief — {Topic Title}

> **What this document is**
>
> The **shortest** document in the `specs/` folder. Answers "why does this
> topic exist, what problem is it solving, and what does done look like?".
>
> Audience: stakeholders, new joiners, code reviewers who need context.
>
> One Epic Brief per topic. Keep it under ~200 lines. Does not describe *how*
> — that is the Architecture Spec, Core Flows, and Implementation Spec.
>
> Delete this instruction block from the final document.

---

## What We're Building

{1 paragraph — the elevator pitch. What outcome does this topic deliver for
the product / users / developers? Be concrete.}

---

## Context & Problem

**Who's affected:** {users? admins? developers? specify}

**Where in the product:** {component / module / page / backend service — use
`file:` refs where helpful}

**The current pain:**

- **{pain point 1}**: {1–2 sentences on why it hurts today}
- **{pain point 2}**: {...}
- **{pain point 3}**: {...}

---

## Scope

### What This Epic IS

- {in-scope outcome 1}
- {in-scope outcome 2}
- {in-scope outcome 3}

### What This Epic Is NOT

- Not {explicitly-excluded concern} — {rationale or "future scope"}
- Not {...}

---

## Success Criteria

The outcomes that define "done" for the whole topic. Written for a non-engineer
reader.

- {user-visible or measurable success criterion 1}
- {user-visible or measurable success criterion 2}
- {user-visible or measurable success criterion 3}

---

## Constraints & Non-Functionals

- **Performance:** {any perf budget, e.g. "bundle size must not grow > 5%"}
- **Compatibility:** {browser / OS / Angular version / etc}
- **Security / Privacy:** {any known constraint}
- **Accessibility:** {WCAG level, screen-reader coverage, etc}
- **SEO / SSR:** {any known constraint}

---

## Dependencies & Related Work

- **Depends on:** {other topics / external systems}
- **Blocks:** {other topics that must wait for this}
- **Related (not blocking):** {...}

---

## External References

- {Linear / Jira / issue tracker links}
- {Client feedback document / thread}
- {External standards / RFCs / research references}

---

## Links

- [Codebase Investigation](./Codebase_Investigation_—_{...}.md)
- [Architecture Spec](./Architecture_Spec_—_{...}.md)
- [Core Flows](./Core_Flows_—_{...}.md)
- [Implementation Spec](./Implementation_Spec_—_{...}.md)
- [Execution Plan](./Execution_Plan_—_{...}.md)
