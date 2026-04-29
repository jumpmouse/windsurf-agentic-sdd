---
description: Simple status check — where are we, what's active, what's next. If everything is done, drop a Bugs Bunny.
---

# /kickoff — Where Are We?

A friendly status report. Reads the latest activity from the docs system, identifies any active topic / ticket, and suggests the next step. **No chain-routing.** No deep analysis. Just a quick "you are here" so you can decide what to do next.

## Inputs

- None required. Optional: `TOPIC_SLUG` to focus on a specific topic.

## Steps

### 1. Read project pulse

Read (in order):

- `docs/WORK_LOG.md` — most recent topic section + most recent session entry.
- The active topic's `docs/{TOPIC_SLUG}/README.md` — Status table.
- The active topic's `docs/{TOPIC_SLUG}/CHANGELOG.md` — most recent date section.

If `docs/WORK_LOG.md` does not exist, the docs system has not been initialized — STOP and suggest `/init-docs`.

### 2. Identify active state

From what was read:

- **Active topic** — the topmost topic in `WORK_LOG.md`, or the one in the most recent session entry.
- **Active ticket** — the most recent in-progress ticket from the topic's Status table (status not `✅ Complete`).
- **Phase signal** — what's the next un-checked phase, if any?
- **Last session** — what type was it (research / specs / planning / tickets / implementation / verification / closure)?

Don't go deep — a 30-second glance is enough.

### 3. Report the picture

Format:

```
👋 Where We Are

📂 Active topic: {TOPIC_SLUG} — {topic title}
🎫 Active ticket: {TICKET_ID} — {ticket title} ({status})
📅 Last session: {YYYY-MM-DD} — {session type}
🧭 Phase: {phase name} ({X}/{Y} done)

💬 Last thing that happened:
- {one-line summary of most recent CHANGELOG entry or session note}
```

### 4. Suggest the next step

Based on the active state, propose ONE next workflow (not a routing decision — a friendly suggestion):

| Active state | Suggested next |
|--------------|----------------|
| Topic exists, no research yet, no specs | `/topic-research` |
| Research done, no 5-spec set | `/topic-specs-from-research` |
| 5 specs done, no Execution Plan | `/topic-spec-plan` |
| Execution Plan done, no tickets | `/topic-tickets-from-plan` |
| Tickets generated, no verification report | `/topic-tickets-verify` |
| Active ticket with no implementation commits | `/topic-implement {TICKET_ID}` |
| Implementation done, no verification report | `/topic-verify {TICKET_ID}` |
| Verification done, no completion report | `/topic-close {TICKET_ID}` |
| Everything in this topic complete, more tickets queued | `/kickoff {NEXT_TICKET_ID}` |
| All tickets in all topics complete | (see Step 5 — Bugs Bunny mode) |

Output:

```
🎯 Suggested next: {workflow with args}
   Reason: {one-liner from the table}
```

The user is in charge. They decide whether to follow the suggestion.

### 5. Bugs Bunny mode (if everything is done)

If there is nothing in progress, no pending tickets, and no active topic, output a single absurd, in-character one-liner — random Bugs-Bunny-style cartoon character voice. Examples (do not copy verbatim — generate a fresh one):

```
🥕 Eh, what's up, doc? Looks like we're all caught up. The repo is so clean it squeaks. Maybe start something new with /topic-new — or take five and have a carrot. 🐰
```

```
🦆 Despicable! Nothing left to do! You'd think there'd be a bug somewhere... but no. Everything ✅. Spin up a new topic with /topic-new, or step away from the keyboard, you maniac.
```

Pick the voice; keep it short; one emoji + one sentence is plenty.

### 6. STOP

Do NOT auto-launch any workflow. Do NOT chain. The user reads the picture and decides what to do next.

## Notes

- This is a **status check**, not a router. It does not decide for the user.
- For new work from a problem statement, the user should run `/topic-new` to create a topic, then `/topic-research` with `PROBLEM`.
- For an existing topic with no work in progress, the user runs `/kickoff` to see where things stand, then picks the next workflow from the suggestion.
