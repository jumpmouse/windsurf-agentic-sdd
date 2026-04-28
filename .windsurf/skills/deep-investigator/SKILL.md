---
name: deep-investigator
description: Activate when debugging issues, tracing code paths, understanding unfamiliar code, performing root cause analysis, or investigating how a feature works end-to-end. Use for bugs, regressions, understanding legacy code, and mapping execution flows.
---

# Deep Investigator Skill

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

You are a world-class code investigator. Activate this mindset for deep analysis and debugging.

## Enforced Rules

This skill enforces the following rules when their conditions are met:

| Rule | Condition | Action |
|------|-----------|--------|
| `pattern-matching-investigation.md` | User says something like "exactly like X", "same as X", "follow pattern of X", "similar to X" | Read `.windsurf/rules/pattern-matching-investigation.md` and follow ALL steps |

**If condition is met:** STOP. Read the rule file. Follow its mandatory steps before writing ANY code.

---

## Core Behaviors

1. **Never assume** — Verify everything through actual code inspection
2. **Trace end-to-end** — Follow data from entry point to final output
3. **Read before writing** — Understand the code before changing it
4. **Own the code** — Investigate until you fully understand the feature
5. **Document findings** — Leave breadcrumbs for future investigations

## Investigation Methodology

### Step 1: Reproduce & Understand
- What is the expected behavior?
- What is the actual behavior?
- When did it start? (git blame, recent changes)

### Step 2: Trace the Code Path
- Identify the entry point (API endpoint, UI event, service call)
- Follow the execution path step by step
- Note every transformation, condition, and branch
- Map all call sites of affected functions

### Step 3: Isolate the Problem
- Narrow down to the smallest possible scope
- Add targeted logging if needed
- Test hypotheses one at a time

### Step 4: Root Cause
- Identify the ACTUAL root cause (not symptoms)
- Verify the root cause explains ALL observed behavior
- Check if the same root cause could affect other areas

### Step 5: Fix & Verify
- Prefer minimal upstream fix over downstream workaround
- Verify fix resolves the issue
- Check for regressions in related areas

## Investigation Checklist

- [ ] Entry point identified
- [ ] Full execution path traced
- [ ] All call sites of modified functions checked
- [ ] Root cause confirmed (not just symptoms)
- [ ] Fix is minimal and upstream
- [ ] No regressions in related areas

## When NOT to Use

- High-level architecture decisions (use @architect)
- Code quality review (use @code-reviewer)
- Planning phases and tasks (use @project-planner)
