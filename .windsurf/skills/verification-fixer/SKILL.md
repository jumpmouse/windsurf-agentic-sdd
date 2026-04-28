---
name: verification-fixer
description: Activate when reading and fixing issues from a verification report, code review document, or plan-vs-implementation check. Use for implementing targeted fixes based on categorized comments (Critical, Major, Minor), prioritizing by severity, and ensuring fixes don't introduce regressions. Also use for verifying a plan against investigation findings and actual code.
---

# Verification Fixer Skill

## 🌐 Project Override Hook

This is a **global skill**. Before applying it, check whether the host project provides an override.

**Where to look** — in the host project's workspace root (NOT in the global skills repo):

```
.windsurf/overrides/skills/<same-skill-folder-name>/SKILL.md
```

Use the same skill folder name as this file's parent directory (e.g. for `.windsurf/skills/architect/SKILL.md`, look for `.windsurf/overrides/skills/architect/SKILL.md`).

**If that override file exists**, read it and apply the first matching mode (declared in frontmatter as `mode: replace|extend|modify`, OR via a first-heading marker `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — IGNORE the guidance below; apply ONLY the override's procedure.
- **EXTEND** — apply this skill as written, then layer the override's additional behaviors / heuristics on top.
- **MODIFY** — apply this skill, but with the sections the override specifies substituted or refined (typically by section heading).

The override skill folder may also contain its own supporting files (scripts, templates) — treat them with the same precedence as the override `SKILL.md`.

**If no override file exists**, apply this skill as written. If the override's mode is missing or ambiguous, **STOP and ask the user** which mode applies before proceeding.

---

You are a precise verification issue resolver. Activate this mindset when fixing issues from verification reports, review documents, or plan-vs-code audits.

## Enforced Rules

This skill enforces the following rules when their conditions are met:

| Rule | Condition | Action |
|------|-----------|--------|
| `pattern-matching-investigation.md` | User says something like "exactly like X", "same as X", "follow pattern of X", "similar to X" | Read `.windsurf/rules/pattern-matching-investigation.md` and follow ALL steps |

**If condition is met:** STOP. Read the rule file. Follow its mandatory steps before writing ANY code.

---

## Core Behaviors

1. **Read the full report first** — Understand all issues before fixing any
2. **Fix by severity** — Critical → Major → Minor → skip Outdated
3. **Atomic fixes** — One issue at a time, verify each before moving on
4. **No new functionality** — Fix only what the report says, nothing more
5. **Regression awareness** — Each fix must not break what already works

## Severity Priority

| Severity | Action | Approach |
|----------|--------|----------|
| **Critical** | Must fix first | Blocks core functionality. Fix immediately. |
| **Major** | Should fix | Significant issues. Fix after all criticals. |
| **Minor** | Fix if time | Polish items. Fix after majors. |
| **Outdated** | Skip | No longer relevant. Mark as skipped. |

## Fix Process

### Step 1: Parse Report
- Read the entire verification/review document
- Count issues by severity
- Identify dependencies between issues (fix A before B)
- Report: total issues, breakdown by severity, estimated effort

### Step 2: Fix Each Issue
For each issue (in severity order):
1. **Read** — Understand the problem and affected files
2. **Investigate** — Check the code area, understand context (@deep-investigator if needed)
3. **Fix** — Implement only what the report describes
4. **Verify** — Confirm fix resolves the issue without side effects
5. **Track** — Mark as resolved in progress tracking

### Step 3: Self-Check
After all fixes:
- Review all changes together for consistency
- Verify no regressions in related areas
- Compare fix count against report count
- List any issues intentionally skipped with reason

## Progress Tracking Format

```markdown
### Verification Fixes Round {N}
- [x] 🔴 Critical: {description} — Fixed in {file}
- [x] 🟡 Major: {description} — Fixed in {file}
- [x] 🔵 Minor: {description} — Fixed in {file}
- [~] ⚪ Outdated: {description} — Skipped (no longer relevant)
```

## When NOT to Use

- Implementing new features from handoff (use @spec-implementer)
- Debugging unknown issues (use @deep-investigator)
- Architecture decisions (use @architect)
- Planning work (use @project-planner)

## Quality Gates

- [ ] All Critical issues resolved
- [ ] All Major issues resolved
- [ ] Minor issues addressed or explicitly deferred
- [ ] No regressions introduced
- [ ] Fix count matches report count
- [ ] PLAN.md updated with verification section
