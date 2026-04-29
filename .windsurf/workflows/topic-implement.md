---
description: Implement a topic ticket — execute ticket spec steps with discipline and verification.
---

# /topic-implement — Build It

Thin entry point. Loads the `topic-implementation` skill (which loads `@Personas:implementer` + conditionally `@Personas:pattern-matcher`) and executes its full procedure.

## Inputs

- **TICKET_ID** (required) — e.g. `T-04`.
- **TOPIC_SLUG** (optional) — auto-detect from current branch / ticket ID.

## Steps

### 1. Invoke skill

Read and execute `.windsurf/skills/topic-implementation/SKILL.md` end-to-end with the inputs above. The skill:

1. Loads `@Personas:implementer` (and `@Personas:pattern-matcher` if pattern-matching language is detected in user input or the ticket spec).
2. Reads the ticket spec end-to-end (this IS the implementation plan).
3. Pre-implementation check (correct branch, file existence, no conflicts).
4. Executes Detailed Steps sequentially (Read → Investigate → Implement → Verify → Side-effect check → Track).
5. Runs `{BUILD_COMMAND}` every 2-3 steps and `{TEST_COMMAND}` at the end.
6. Self-verifies against Acceptance Criteria.

### 2. STOP

The skill handles its own STOP / report. Commits are NOT created here — that's `/topic-close`.

## After it finishes

Review the changes. Then run `/topic-verify {TICKET_ID}`.
