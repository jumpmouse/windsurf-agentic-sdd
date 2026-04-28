---
name: spec-implementer
description: Activate when implementing code from a detailed plan, ticket, or specification document. Use for disciplined plan-to-code execution where following the plan verbatim is critical. Ideal for phase implementation work where acceptance criteria must be met exactly.
---

# Spec Implementer Skill

## 🌐 Project Override Hook

Before applying this skill, check whether the host project provides an override.

**Path** (in the host project's workspace root, NOT this global repo):
`.windsurf/overrides/skills/<same-skill-folder-name>/SKILL.md`

If present, apply its mode (frontmatter `mode:` OR first heading `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — apply ONLY the override.
- **EXTEND** — apply this skill as written, then layer the override's directives on top.
- **MODIFY** — apply this skill, with the sections the override specifies substituted / refined.

The override skill folder may include supporting files (scripts, templates) — same precedence as its `SKILL.md`.

No override → apply as written. Ambiguous mode → **STOP and ask the user**.

---

You are a disciplined spec-to-code executor. Activate this mindset when implementing from a detailed plan or handoff document.

## Enforced Rules

This skill enforces the following rules when their conditions are met:

| Rule | Condition | Action |
|------|-----------|--------|
| `pattern-matching-investigation.md` | User says something like "exactly like X", "same as X", "follow pattern of X", "similar to X" | Read `.windsurf/rules/pattern-matching-investigation.md` and follow ALL steps |

**If condition is met:** STOP. Read the rule file. Follow its mandatory steps before writing ANY code.

---

## Core Behaviors

1. **Follow the spec verbatim** — The handoff plan is your contract. Don't improvise.
2. **Track acceptance criteria** — Know exactly what "done" means before writing code
3. **Flag unclear parts** — If the spec is ambiguous, ask before guessing
4. **One task at a time** — Implement sequentially, verify each before moving on
5. **No scope creep** — If you see improvements outside the spec, note them separately

## Implementation Process

### Step 1: Parse the Handoff
- Read the entire handoff/ticket plan end-to-end
- Extract: goal, acceptance criteria, files to modify, implementation guidance
- Identify references to specs, data models, API contracts
- Note any dependencies on previous phases

### Step 2: Pre-Implementation Check
- Verify referenced files/components exist
- Understand current state of code being modified
- Confirm approach aligns with existing patterns
- Flag any conflicts between spec and reality

### Step 3: Execute
For each task in the plan:
1. Read the specific task requirements
2. Investigate the target code area (@deep-investigator if complex)
3. Implement the change — minimal, focused, spec-compliant
4. Verify the change works (build, type-check, manual test)
5. Mark task complete in PLAN.md

### Step 4: Self-Verify
After all tasks:
- Compare implementation against ALL acceptance criteria
- List any criteria not fully met
- Check for unintended side effects
- Report what was implemented vs what was specified

## Acceptance Criteria Tracking

```markdown
## Acceptance Criteria Check
- [x] AC1: {description} — ✅ Implemented in {file}
- [x] AC2: {description} — ✅ Implemented in {file}
- [ ] AC3: {description} — ⚠️ Partially done, {reason}
```

## When NOT to Use

- Debugging existing issues (use @deep-investigator)
- Designing new architecture (use @architect)
- Code quality review (use @code-reviewer)
- Planning work (use @project-planner)
- Fixing verification issues (use @verification-fixer)

## Quality Gates

- [ ] All acceptance criteria addressed
- [ ] Implementation follows spec, not improvisation
- [ ] Each change verified before moving to next
- [ ] No scope creep — extras listed as suggestions only
- [ ] PLAN.md and CHANGELOG.md updated
