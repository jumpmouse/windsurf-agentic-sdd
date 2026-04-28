---
description: Deep investigation for a complex topic ticket — map files, verify assumptions, surface risks
---

# TOPIC RESEARCH — Ticket Investigation

## 🌐 Project Override Hook

Before executing this workflow, check whether the host project provides an override.

**Path** (in the host project's workspace root, NOT this global repo):
`.windsurf/overrides/workflows/<same-filename-as-this-file>.md`

If present, apply its mode (frontmatter `mode:` OR first heading `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — run ONLY the override; ignore the steps below.
- **EXTEND** — run the steps below as written, then run the override as a final phase (or at the position it specifies).
- **MODIFY** — run the steps below, with the steps the override declares substituted / inserted at the matching positions.

No override → proceed as written. Ambiguous mode → **STOP and ask the user**.

---

> ⚠️ **MANDATORY EXECUTION PROTOCOL**
> This is a **strict step-by-step workflow**, NOT background context.
> - Execute steps **in order**, one at a time
> - **Do NOT skip steps** — every step exists for a reason
> - **Do NOT combine or optimize** steps unless explicitly allowed
> - **Do NOT treat this as context** to inform freeform work
> - After completing each step, move to the next step
> - If a step says to execute another workflow, **execute it fully** before continuing with this one
> - If you finished all steps here AND this workflow is called from parent workflow, get back to executing parent workflow steps.
> - If you finished all steps AND there are no parent workflow, mandatory is to stop and report to the user. Do not jump to "logical" next step!
>
> **Violation of this protocol undermines the entire development system.**

Deep investigation of a complex topic ticket before implementation. Verifies the ticket spec's assumptions against actual code, maps the blast radius, and surfaces risks.

Use @deep-investigator mindset throughout.

## Inputs

- **TICKET_ID**: e.g. `T-04`

## Steps

### 0. Load Skill

// turbo
**MANDATORY — Do this FIRST before any other work.**
Read and internalize the skill file:
- Read `.windsurf/skills/deep-investigator/SKILL.md`

### 1. Read Ticket Spec

// turbo
Read the full ticket:
- `docs/{TOPIC_SLUG}/tickets/{TICKET_ID}__*.md`
- Any dependency ticket reports referenced in the spec
- Relevant specs from `docs/{TOPIC_SLUG}/specs/`

Extract:
- All files mentioned in the ticket
- All acceptance criteria
- All guardrails/constraints
- Assumed current state of code

### 2. Verify Assumptions Against Code

For every file and assumption in the ticket spec:

1. **Read the file** — Does it exist? Does it match what the ticket assumes?
2. **Check current state** — Has something changed since the ticket was written?
3. **Map call sites** — For functions that will change, find ALL callers
4. **Check dependencies** — What imports/uses the code being modified?
5. **Verify patterns** — Does the codebase handle similar things consistently?

Track findings:
```markdown
## Assumption Verification
- [x] {file}: matches ticket assumption ✅
- [ ] {file}: ⚠️ ticket assumes {X} but actual code is {Y}
```

### 3. Map Blast Radius

Beyond what the ticket explicitly lists:
- What other files import/use the affected modules?
- Are there templates that reference changed selectors/components?
- Are there specs that test the affected behavior?
- Could the change affect SSR or lazy-loaded modules?

### 4. Identify Risks & Gaps

```markdown
## Risks
- {Risk}: {what could go wrong} — {suggested mitigation}

## Gaps in Ticket Spec
- {File/scenario not covered by the ticket}

## Deviations from Spec
- {Where actual code differs from ticket's assumptions}
```

### 5. Formulate Questions

If anything needs user input before implementation:
```markdown
## Questions for User
1. {Question about scope, approach, or priority}
```

If no questions — state research is complete and ready for implementation.

### 6. Quality Gate

Before reporting, verify:
- [ ] Every file in the ticket spec has been read
- [ ] All call sites of key functions/modules traced
- [ ] Dependencies and side effects mapped
- [ ] Spec assumptions verified against actual code
- [ ] Risks documented

### 7. Report

```
✅ Research Complete — {TICKET_ID}

🔍 Findings:
- Files verified: {X}/{Y} match ticket assumptions
- Additional files affected: {count}
- Risks identified: {count}
- Spec deviations: {count}
- Questions: {count}

🎯 Next: User reviews → /topic-implement {TICKET_ID}
```

---

## Notes

- This workflow **STOPS** after reporting — user reviews findings before implementation
- Do NOT start implementation in this workflow
- Findings stay in the conversation — no separate research document created
- If the ticket scope seems larger than expected, say so — user decides whether to split
- After user review, call `/kickoff {TICKET_ID}` to resume (routes to implement)
