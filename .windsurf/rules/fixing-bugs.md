---
trigger: always_on
---


## 🌐 Project Override Hook

Once per session (or whenever this rule's content is loaded into context), check whether the host project provides an override.

**Path** (in the host project's workspace root, NOT this global repo):
`.windsurf/overrides/rules/<same-filename-as-this-file>.md`

If present, apply its mode (frontmatter `mode:` OR first heading `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — follow ONLY the override.
- **EXTEND** — follow this rule AND the override.
- **MODIFY** — follow this rule with the bullets / sections the override substitutes / refines.

No override → follow as written. Ambiguous mode → follow the global rule and surface the ambiguity to the user.

---

1. fixing bugs:
  - when you find a bug, fix it, if it is in the scope of the current task
  - if it is not in the scope of the current task, inform me about it, and provide what should be done to fix it
  - while fixing a bug, if you don't manage to fix it during 3 itterations, stop work, report what you have done, what is the problem, what you tried and what are the options to fix it
