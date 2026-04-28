# Project-Local Overrides for Global Windsurf Assets

NOTE: if you want to use per-project Windsurf overrides, copy this folder to the root of your project (github repo) and remove suffix from folder name (rename folder to .windsurf - delete "-project-overrides" part)

This folder lets you **extend, modify, or replace** any global Windsurf
workflow / skill / rule for this specific project, without renaming it
or duplicating the whole file.

The mechanism is implemented as an explicit hook in every global
asset — see `.windsurf/{workflows,skills,rules}/...` in the global
repo (`windsurf-agentic-sdd`). Each global asset has a section titled
**🌐 Project Override Hook** that tells the AI client to look here.

## Folder Structure

```
.windsurf/overrides/
├── workflows/    ← override files for global workflows
├── skills/       ← override folders for global skills
└── rules/        ← override files for global rules
```

## Naming Convention

The override file MUST have the **same name** as the global asset it
overrides:

| Global asset | Override path |
|---|---|
| `.windsurf/workflows/kickoff.md` | `.windsurf/overrides/workflows/kickoff.md` |
| `.windsurf/skills/architect/SKILL.md` | `.windsurf/overrides/skills/architect/SKILL.md` |
| `.windsurf/rules/investigation.md` | `.windsurf/overrides/rules/investigation.md` |

The AI client uses filename / folder-name matching to find the override.
There is no registry, no manifest — just put a file in the right place.

## Override Modes

Every override file declares ONE of three modes — in YAML frontmatter
**OR** via a first-heading marker.

### `replace`

Replace the global asset entirely. The global is fully shadowed; only
the override runs / applies.

```markdown
---
mode: replace
---

# Custom Kickoff for This Project

(Full custom workflow / skill / rule content here. The global is
ignored.)
```

Or, equivalently, with a first-heading marker:

```markdown
# REPLACE

# Custom Kickoff for This Project

(Full custom content here.)
```

### `extend`

Run / apply the global asset as written, then layer this override on
top. Use this when the global is fine and you just want to add more
steps / behaviors / constraints.

```markdown
---
mode: extend
---

# Additional Steps for /kickoff in This Project

After Step N of the global workflow, also:

1. Fetch the latest milestone from Linear
2. Cross-reference the ticket against the active sprint
3. Note any milestone-impact in the kickoff report
```

### `modify`

Run / apply the global, but substitute or refine specific
steps / sections. Reference the target by step number or section
heading from the global asset.

```markdown
---
mode: modify
---

# Substitutions for /topic-close in This Project

## Replace Step 3 ("Final review") with:

1. Run the project-specific lint suite (`npm run lint:custom`)
2. Run the project-specific verification (`npm run test:e2e`)
3. Then run the global Step 3 content as written.
```

## What the AI Client Does

When you invoke a global workflow / skill (or when a global rule is
loaded into context), the global asset's **Project Override Hook**
section instructs the AI to:

1. Look for a file at the matching path in `.windsurf/overrides/`.
2. If found, read its mode and apply it accordingly.
3. If not found, run / apply the global as written.
4. If the mode is missing or ambiguous, **STOP and ask the user**.

## Examples

### Add milestone reporting to `/kickoff` (extend)

`.windsurf/overrides/workflows/kickoff.md`:

```markdown
---
mode: extend
---

# Project-specific extensions for /kickoff

After the global workflow's "Detect Ticket" step:

1. Look up the ticket in Linear via the Linear MCP.
2. Note the milestone label on the ticket.
3. Include the milestone in the kickoff report.
```

### Custom `architect` skill for a project that uses microservices (modify)

`.windsurf/overrides/skills/architect/SKILL.md`:

```markdown
---
mode: modify
---

# Architect — additions for this project

## Add to "Design Process":

- Always evaluate impact on the service mesh and the auth boundary.
- Prefer event-driven communication over synchronous RPC where
  latency-tolerant.
```

### Replace the project-wide investigation rule (replace)

`.windsurf/overrides/rules/investigation.md`:

```markdown
---
mode: replace
---

# Investigation discipline (project-specific)

For this codebase:

- All investigation must start from the BFF layer, not the FE.
- Always check the audit log before proposing a fix.
- Do not modify auto-generated files in `src/generated/`.
```

## Notes

- This mechanism is a **convention enforced by the global asset's
  override hook** — Windsurf does NOT natively know about
  `overrides/`. The mechanism only works because every global asset
  includes the hook section that points here.
- Keep override files small and focused. If an override grows large,
  consider whether the global asset itself needs an update instead.
- Override files are committed to this project's repo. They live with
  the project, not with the global repo.
- For the rationale and the documented activation modes that this
  pattern works around, see Windsurf docs on Rules / Workflows / Skills.
