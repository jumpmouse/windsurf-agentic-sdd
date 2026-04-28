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

1. while investigating or researching:
make sure you investigate deeply, end to end, own the code, no assumptions. Pinpoint every detail, every edge case, every possible scenario. And verify it against the code and requirements. So,, specs and requirements should also be verified, since those are created by AI agent also, and should be the source of truth. I want you to be the one who owns the code, not me.