---
name: plantuml
description: Generate a PlantUML sequence diagram from a Windsurf Codemap mention. Maps Codemap traces to architectural layers (frontend route, frontend component, backend, repository, render). Returns a fenced .puml block plus a key-references list. Optional --save and --focus flags.
---

# PlantUML Skill

## Persona

**MANDATORY first action:** Load the Architect persona by reading `.windsurf/personas/architect.md`. Architectural thinking — boundaries, ownership, and flow — is exactly what diagram generation needs.

## Invocation

This skill is consumed when the user mentions a Codemap and wants a sequence diagram. Typical entry points:

```
@Codemap-Title  → please draw a sequence diagram
@Codemap-Title  → /plantuml --save docs/my-flow.puml
@Codemap-Title  → /plantuml --focus backend
```

There is no dedicated `/plantuml` workflow — this skill is invoked directly when its description matches the user's request, or it can be invoked from another workflow that needs a diagram.

## Optional Flags

| Flag | Values | Default | Description |
|------|--------|---------|-------------|
| `--save <path>` | file path | — | Save `.puml` to file |
| `--focus <area>` | `route\|backend\|mapping\|template\|full` | `full` | Bias detail density toward area |

## Required Input

- An `@Codemap` mention in the same message (Codemap metadata must be in conversation context).

## Protocol

**Diagram is generated ONLY from Codemap data — no external file reads unless explicitly requested.**

## Procedure

### 1. Validate input

- Ensure an `@Codemap` mention is present.
- If missing, ask the user to re-run with `@[Codemap Title]`.

### 2. Parse the Codemap

- Read `title`, `description`, `traces[].locations[]` from Codemap payload.
- Do NOT fetch external resources — rely only on embedded locations / notes.

### 3. Identify core flow

Map Codemap traces to architectural layers (adjust if Codemap differs):

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

### 4. Generate PlantUML sequence diagram

Structure:
- Three logical boxes: `Frontend (Angular)`, `Backend (.NET)`, `Frontend Render` (adjust to actual stack from Codemap).
- Participants from each layer identified in Step 3.
- Flow: User → Router → Component → FE Service → HTTP → BE Controller → BE Service → Repository → DB → response back → render mapping → template.

Required elements:
- `note` blocks with file paths and approximate line numbers from Codemap locations.
- `alt` blocks for conditional branches (e.g., element types).
- `loop` blocks for iterations (e.g., page elements).
- `legend` summarizing the flow at the bottom.

### 5. Output

Return:

1. A fenced `plantuml` code block with complete `@startuml` / `@enduml` diagram.
2. A **Key References** list mapping to relevant file paths from Codemap.

### 6. Focus control (if `--focus` provided)

Bias notes and detail density toward the specified area while keeping the full sequence intact.

### 7. Save to file (if `--save` provided)

Create file at the specified path with PlantUML content. Confirm path to user.

## Final Report

```
✅ PlantUML Diagram Generated

📊 Source: @Codemap "{title}"
🎯 Focus: {full | route | backend | mapping | template}
📁 Saved to: {path or "in-thread only"}

🔑 Key References:
- {layer}: file:{path}:{line}
- ...
```

## Error Handling

| Error | Action |
|-------|--------|
| No `@Codemap` mention | Ask user to re-run with `@[Codemap Title]`. |
| Codemap has no traces | Report empty Codemap; ask user to check it. |
| `--save` path invalid | Report error; output diagram to chat instead. |

## Notes

- Avoids opening additional code files — relies on Codemap trace data only.
- For deeper validation, user must explicitly request examining referenced files.
- For image export (PNG / SVG), ask the user about preferred tool (PlantUML CLI, plugin, web renderer).
- Generates `.puml` text, not images.
