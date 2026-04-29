---
trigger: always_on
---

# Project Override Hook — One System, Per-Project Customization

This system is designed to be **shared across multiple host projects**. Each host project may override workflows, skills, personas, or rules **without forking** the system files.

## Where overrides live

In the **host project's workspace root** (NOT in this system repo):

```
.windsurf/overrides/
├── workflows/<same-filename-as-the-system-workflow>.md
├── skills/<same-skill-folder-name>/SKILL.md
├── personas/<same-persona-filename>.md
└── rules/<same-rule-filename>.md
```

The override path mirrors the system path. Filename / folder name must match exactly.

## How overrides apply

Each override declares one of three modes via either YAML frontmatter `mode:` or the file's first heading.

| Mode | Behavior |
|------|----------|
| **REPLACE** | Apply ONLY the override. Ignore the system file entirely. |
| **EXTEND** | Apply the system file as written, then layer the override's directives on top (additive). |
| **MODIFY** | Apply the system file with the sections the override specifies substituted or refined. |

Override file shape:

```markdown
---
mode: EXTEND
---

# Override content here, in the same shape as the system file.
```

## When to check for overrides

When Cascade enters a workflow, skill, persona, or rule from the system, it MUST first check whether the host project provides a matching override at the path above.

- **Override present + mode declared** → apply per the table.
- **Override present + ambiguous mode** → STOP and ask the user.
- **No override** → apply the system file as written.

## Notes

- Overrides are version-controlled inside the host project — that's their value.
- This rule is the single source of truth for the override mechanism. System workflows / skills / personas do NOT each need to repeat this hook in their body. Cascade applies the rule globally because it is `always_on`.
