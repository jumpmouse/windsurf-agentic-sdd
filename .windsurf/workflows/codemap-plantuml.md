---
description: Generate a PlantUML sequence diagram from a Windsurf Codemap mention
---

# CODEMAP PLANTUML

## 🌐 Project Override Hook

Before executing this workflow, check whether the host project provides an override.

**Path** (in the host project's workspace root, NOT this global repo):
`.windsurf/overrides/workflows/<same-filename-as-this-file>.md`

If present, apply its mode (frontmatter `mode:` OR first heading `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — run ONLY the override; ignore the steps below.
- **EXTEND** — run the steps below as written, then run the override as a final phase (or at the position it specifies).
- **MODIFY** — run the steps below, with the steps the override declares substituted / inserted at the matching positions.

No override → proceed as written. Ambiguous mode → **STOP and ask the user**.

---

> ⚠️ **MANDATORY EXECUTION PROTOCOL**
> This is a **strict step-by-step workflow**, NOT background context.
> - Execute steps **in order**, one at a time
> - **Do NOT skip steps** — every step exists for a reason
> - **Do NOT combine or optimize** steps unless explicitly allowed
> - **Do NOT treat this as context** to inform freeform work
> - After completing each step, move to the next step
> - If a step says to execute another workflow, **execute it fully** before continuing with this one
> - If you finished all steps here AND this workflow is called from parent workflow, get back to executing parent workflow steps.
> - If you finished all steps AND there are no parent workflow, mandatory is to stop and report to the user. Do not jump to "logical" next step!
>
> **Violation of this protocol undermines the entire development system.**

Turn a Windsurf Codemap (@Codemap mention) into a ready-to-use PlantUML sequence diagram plus a concise reference list of key files.

**Protocol: Diagram is generated ONLY from Codemap data — no external file reads unless explicitly requested.**

## Invocation

```
/codemap-plantuml @[Your Codemap Title]
/codemap-plantuml @[Your Codemap Title] --save docs/my-flow.puml
/codemap-plantuml @[Your Codemap Title] --focus backend
```

## Optional Flags

| Flag | Values | Default | Description |
|------|--------|---------|-------------|
| `--save <path>` | file path | — | Save .puml to file |
| `--focus <area>` | `route\|backend\|mapping\|template\|full` | `full` | Bias detail density toward area |

## Required Input

- An `@Codemap` mention in the same message (Codemap metadata must be in conversation context)

## Steps

### 1. Validate Input

- Ensure an @Codemap mention is present
- If missing, ask the user to re-run with `@[Codemap Title]`

### 2. Parse the Codemap

- Read `title`, `description`, and `traces[].locations[]` from the Codemap payload
- Do NOT fetch external resources — rely only on embedded locations/notes

### 3. Identify Core Flow

Map Codemap traces to these architectural layers (adjust if Codemap differs):

| Layer | Typical Components | Example Files |
|-------|-------------------|---------------|
| **Frontend Route** | Route handler | `libs/public/src/lib/lib.routes.ts` |
| **Frontend Component** | Page component | `generic-page.component.ts` |
| **Frontend Service** | HTTP call service | `page-data.service.ts` |
| **Backend Controller** | API endpoint | `PageDataController.cs` |
| **Backend Service** | Business logic | `PageDataService.cs` |
| **Repository** | Data access | `AppUnitOfWork.PageRepository` |
| **Database** | SQL Server | — |
| **Frontend Render** | Template mapping | Element-type switch, `*ngFor` |

### 4. Generate PlantUML Sequence Diagram

**Structure:**
- Three logical boxes: `Frontend (Angular)`, `Backend (.NET)`, `Frontend Render`
- Participants from each layer identified in Step 3
- Flow: User → Router → Component → FE Service → HTTP → BE Controller → BE Service → Repository → DB → response back → render mapping → template

**Required elements:**
- `note` blocks with file paths and approximate line numbers from Codemap locations
- `alt` blocks for conditional branches (e.g., element types)
- `loop` blocks for iterations (e.g., page elements)
- `legend` summarizing the flow at bottom

### 5. Output

Return:
1. A fenced `plantuml` code block with complete `@startuml` / `@enduml` diagram
2. A **Key References** list mapping to relevant file paths from Codemap

### 6. Focus Control (if --focus provided)

Bias notes and detail density toward the specified area while keeping the full sequence intact.

### 7. Save to File (if --save provided)

Create file at the specified path with the PlantUML content. Confirm path to user.

---

## Error Handling

| Error | Action |
|-------|--------|
| No @Codemap mention | Ask user to re-run with `@[Codemap Title]` |
| Codemap has no traces | Report empty Codemap, ask user to check it |
| --save path invalid | Report error, output diagram to chat instead |

## Notes

- This workflow avoids opening additional code files — relies on Codemap trace data only
- For deeper validation, user must explicitly request examining referenced files
- For image export (PNG/SVG), ask user about preferred tool (PlantUML CLI, plugin, web renderer)
- This workflow generates `.puml` text, not images
