---
trigger: always_on
---

# Strict Execution Protocol

This protocol governs how Cascade executes any `/workflow` invocation in this system.

// turbo
**MANDATORY — Do this FIRST before any other work.**

Print the following message to the user, exactly:
```
🔒 STRICT EXECUTION MODE
Following mandatory step-by-step protocol. No steps will be skipped. No additional work will be done.
```

## Core rules

- **Strict step-by-step.** Workflows are NOT background context — they are a step list. Execute steps **in order**, one at a time.
- **No skipping.** Do not skip a step. Every step exists for a reason.
- **No combining or "optimizing".** Do not merge two steps into one unless the workflow explicitly says you may.
- **No freeform interpretation.** Do not treat the workflow body as inspiration. Treat it as a contract.
- **Sub-workflows are blocking.** When a step says "execute `/another-workflow`", run that workflow fully before continuing the current one.
- **Stop at the end.** When a workflow's last step finishes:
  - If a parent workflow called it, return control to the parent.
  - If there is no parent, **STOP and report to the user**. Do NOT auto-jump to a "logical next step".
- **STOP at user-review gates.** Workflows that end at a "user-review gate" require the user's explicit go-ahead before the next workflow runs.

## Baby steps + 3-failure rule

- Break tasks into atomic, verifiable steps.
- Execute and verify one step at a time.
- If you fail to make progress on the same step after 3 attempts, **STOP** and report what you tried, what failed, and what options remain. Do not silently keep trying.

## Investigation discipline (before changing code)

- Trace all call sites of any function/component you intend to modify.
- Understand the original developer's intent before changing logic.
- Map the execution path before applying a fix.
- Never assume — verify every claim against the actual code.

## Minimal changes

- Modify only what's necessary to satisfy the request.
- Follow existing conventions; do NOT refactor or rename unless the user asked.
- Do not add empty-line cleanups, unrelated edits, or "while I was here" improvements.

## Verification

- After implementing, confirm the result matches the spec or ticket.
- Check for side effects in related areas.
- Never mark a task complete without verification.

**Violating this protocol undermines the entire system.** When in doubt, STOP and ask the user.
