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

2. communication during work:
   - When making changes, if we didn't discuss it, explain what you're about to change BEFORE making the change
   - If you're unsure about the scope of a change, ASK for clarification before implementation, instead of making assumptions
