# Template Examples (Reference)

This folder contains **filled-in worked examples** of the templates in the parent `_templates/` folder. They are **NOT** the templates themselves — workflows reference the generic templates in the parent folder.

## Why this folder exists

When the global Windsurf agentic-SDD repo was first authored, several templates were drafted while working on a real project (the SXE User Frontend Bootstrap 4→5 / Angular migration). The draft templates contained that project's specific content as a worked example.

When the global repo was generalized to be project-agnostic, those drafts were preserved here as **reference examples** — useful for new users to see what a "fully filled" template looks like in practice — and the templates in `_templates/` were rewritten with generic placeholders.

## Files in this folder

| File | Generic counterpart | What the example illustrates |
|------|---------------------|------------------------------|
| `daily-report__sxe-angular-21.md` | `../daily-report-template.md` | Daily progress report for an Angular 15→21 migration session |
| `stakeholder-report__sxe-bootstrap.md` | `../stakeholder-report-template.md` | Non-technical stakeholder narrative for a Bootstrap 4→5 migration |
| `ticket-report__sxe.md` | `../ticket-report-template.md` | Per-ticket completion report |
| `ticket__sxe-bootstrap-migration.md` | `../ticket-template.md` | Full ticket spec with Bootstrap migration steps, shim markers, and Angular `ng build` / `ng test` verification commands |
| `work-log-entry__sxe-bootstrap-migration.md` | `../work-log-entry-template.md` | Topic section in `WORK_LOG.md` for the Bootstrap migration topic |

## How to use them

- **As a new user** — read these alongside the generic template to see what each placeholder slot resolves to in a real project.
- **As a workflow author** — DO NOT reference these from workflows. Always reference the generic templates in `_templates/`.
- **When updating the generic templates** — keep these as a reality-check: a real-world filled version of every template should still look reasonable.
