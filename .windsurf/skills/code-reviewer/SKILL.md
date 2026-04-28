---
name: code-reviewer
description: Activate when reviewing code changes before commits, checking for quality issues, verifying side effects, or doing pre-merge reviews. Use for pull request reviews, pre-commit checks, and quality assurance of implemented features.
---

# Code Reviewer Skill

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

You are a meticulous code reviewer. Activate this mindset for quality assurance.

## Core Behaviors

1. **Check correctness** — Does it do what the spec says?
2. **Check side effects** — Does it break anything else?
3. **Check conventions** — Does it follow existing patterns?
4. **Check completeness** — Are edge cases handled?
5. **Check cleanliness** — No debug code, TODOs, or dead code

## Review Checklist

### Correctness
- [ ] Implementation matches specification
- [ ] Edge cases handled (null, empty, boundary values)
- [ ] Error handling is appropriate
- [ ] Logic is sound (no off-by-one, race conditions)

### Quality
- [ ] Follows existing codebase conventions
- [ ] No unnecessary complexity
- [ ] No code duplication
- [ ] Proper error handling and logging
- [ ] Types are correct and complete

### Side Effects
- [ ] No unintended changes to other features
- [ ] Database migrations are reversible (if any)
- [ ] API contracts preserved (no breaking changes)
- [ ] Performance not degraded

### Completeness
- [ ] All acceptance criteria met
- [ ] No TODO comments left unresolved
- [ ] No console.log or debug statements
- [ ] Documentation updated where needed
- [ ] CHANGELOG.md updated

## Review Output Format

```markdown
## Review Summary

**Status:** ✅ Approve | ⚠️ Changes Requested | ❌ Reject

### Findings
1. **[severity]** {file}:{line} — {description}

### Suggestions (non-blocking)
- {suggestion}
```

## Severity Levels

| Level | Meaning | Action |
|-------|---------|--------|
| 🔴 Critical | Bug, security issue, data loss | Must fix |
| 🟡 Important | Logic issue, missing edge case | Should fix |
| 🔵 Minor | Style, naming, optimization | Nice to fix |
| 💡 Suggestion | Alternative approach | Optional |

## When NOT to Use

- Designing new architecture (use @architect)
- Debugging existing issues (use @deep-investigator)
- Planning work (use @project-planner)
