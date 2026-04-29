---
name: topic-implementation
description: Execute a ticket spec — read the ticket as the implementation plan, run its Detailed Steps with discipline, verify against Acceptance Criteria, run build + tests.
---

# Topic Implementation Skill

## Persona

**MANDATORY first action:** Load the Implementer persona by reading `.windsurf/personas/implementer.md`. Adopt it for the duration of this skill. If pattern-matching language appears in user input or ticket spec ("exactly like X", "same as Y"), also load `.windsurf/personas/pattern-matcher.md`.

## Inputs

- **TICKET_ID** (required) — e.g. `T-04`.
- **TOPIC_SLUG** (optional) — auto-detect from current branch / most recently modified `docs/{slug}/tickets/{TICKET_ID}__*.md` if not provided.

## Output

- Code changes implementing the ticket.
- Updated todo / progress tracking in chat.
- Build + test results.
- No commits (commits are `/topic-close`'s job).

## Procedure

### 1. Read ticket spec

Read end-to-end:

- `docs/{TOPIC_SLUG}/tickets/{TICKET_ID}__*.md` — this IS the implementation plan.
- Dependency tickets' reports if needed.
- Research findings from conversation (if `/topic-research` was run earlier).

Extract:
- **Detailed Steps** — ordered list of implementation tasks.
- **Acceptance Criteria** — conditions for "done".
- **Guardrails** — what NOT to do.
- **Files affected** — every file mentioned.

### 2. Pre-implementation check

Before writing any code:

1. Verify you are on the correct feature branch for this topic. The host project's convention applies (typically captured in `AGENTS.md` at project root). If unclear, **ASK**.
2. Verify referenced files exist and match expected state.
3. Check for uncommitted changes from other work.
4. If research was done, check that findings don't conflict with ticket steps.

Flag any conflicts between spec and reality — **ASK before proceeding**.

### 3. Execute steps

For each step in the ticket's "Detailed Steps" section, sequentially (never skip):

Per the Implementer persona's loop: **Read → Investigate (if unclear) → Implement → Verify → Check side effects → Track**.

**Three-tier boundaries** (from the persona):
- ✅ Always do: follow ticket spec, verify each change, track progress.
- ⚠️ Ask first: any deviation from spec, new dependencies, changes to files not in the ticket.
- 🚫 Never do: scope creep, skip verification, assume a change is safe without checking.

### 4. Ongoing verification

After every 2-3 steps, run the project's build command (defined in the host project's root `AGENTS.md` — typically anchored as `{BUILD_COMMAND}`):

```bash
{BUILD_COMMAND}
```

If build fails, fix before proceeding.

### 5. Self-verify against Acceptance Criteria

After all steps complete:

```markdown
## Acceptance Criteria Check
- [x] AC1: {description} — ✅ Verified in `file:path/to/x.ts`
- [x] AC2: {description} — ✅ Verified in `file:path/to/y.ts`
- [ ] AC3: {description} — ⚠️ {issue or question}
```

Run the project's full test suite (defined as `{TEST_COMMAND}` in `AGENTS.md`):

```bash
{TEST_COMMAND}
```

## Final Report

```
✅ Implementation Complete — {TICKET_ID}

📊 Results:
- Steps completed: {X}/{Y}
- Files modified: {count}
- Acceptance criteria: {X}/{Y} met
- Build: ✅ / ❌
- Tests: {X} passed, {Y} skipped

⚠️ Issues (if any):
- {unresolved items}

💡 Suggestions (out of scope):
- {improvements noticed but not implemented}

🎯 Next: User reviews → /topic-verify {TICKET_ID}
```

Then **STOP**. Do not auto-route — user reviews implementation. Commits are NOT created here — that's `/topic-close`.
