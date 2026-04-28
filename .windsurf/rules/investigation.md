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

1. while investigating or researching:
make sure you investigate deeply, end to end, own the code, no assumptions. Pinpoint every detail, every edge case, every possible scenario. And verify it against the code and requirements. So,, specs and requirements should also be verified, since those are created by AI agent also, and should be the source of truth. I want you to be the one who owns the code, not me.