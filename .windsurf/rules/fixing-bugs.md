---
trigger: always_on
---


## 🌐 Project Override Hook

This is a **global rule**. Once per session (or whenever this rule's content is loaded into context), check whether the host project provides an override.

**Where to look** — in the host project's workspace root (NOT in the global rules repo):

```
.windsurf/overrides/rules/<same-filename-as-this-file>.md
```

Use the same filename as this rule file's basename.

**If that override file exists**, read it and apply the first matching mode (declared in frontmatter as `mode: replace|extend|modify`, OR via a first-heading marker `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — IGNORE the rule below; follow ONLY the override.
- **EXTEND** — follow the rule below AND additionally follow the override's directives.
- **MODIFY** — follow the rule below, but with the specific bullets / sections the override substitutes or refines.

**If no override file exists**, follow the rule below as written. If the override's mode is missing or ambiguous, follow the global rule as-is and surface the ambiguity to the user when convenient.

---

1. fixing bugs:
  - when you find a bug, fix it, if it is in the scope of the current task
  - if it is not in the scope of the current task, inform me about it, and provide what should be done to fix it
  - while fixing a bug, if you don't manage to fix it during 3 itterations, stop work, report what you have done, what is the problem, what you tried and what are the options to fix it
