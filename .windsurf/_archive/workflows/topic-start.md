---
description: Start work on a topic — create branch from Linear and prepare development environment
---

# TOPIC START

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

Prepare the development environment for working on a specific topic. Creates Git branches in both repos, verifies clean state, and confirms readiness.

**Protocol: Both repos get the same branch. Working tree must be clean before starting.**

## Required Inputs

- **TOPIC_SLUG**: The topic folder name (e.g., "carousel-buttons")
- **LINEAR_ISSUE_ID**: The primary Linear issue ID (e.g., "LAN-163")

## Repo Paths

| Repo | Path |
|------|------|
| FE | `/Users/milosbr/projects/lot/fe/landOfTales` |
| BE | `/Users/milosbr/projects/lot/be` |

## Steps

### 1. Read Topic Documentation

// turbo
Read the topic's documentation to understand the work:
1. `docs/{TOPIC_SLUG}/README.md` — overview, Linear issues, branch name
2. `docs/{TOPIC_SLUG}/PLAN.md` — phases and task status
3. `docs/{TOPIC_SLUG}/DISCOVERY.md` — research findings (if exists)

If topic folder doesn't exist, report error and suggest running `/topic-new` first.

### 2. Determine Branch Name

**Option A — From Linear:** Fetch issue via `mcp2_get_issue` to get the git branch name.

**Option B — Generate:** Standard format:
```
{linear-issue-id-lowercase}-{issue-title-kebab-case}
```
Example: `lan-163-the-carousels-left-and-right-button`

**Option C — User-provided:** If user says branch exists, use their name.

**Option D — Detect:** Search existing branches:
```bash
git branch -a | grep -i {LINEAR_ISSUE_ID_LOWERCASE}
```

### 3. Pre-Flight Checks

**CRITICAL: NEVER commit directly to `dev` or `main`. Always use feature branches.**

For EACH repo (FE and BE), verify clean state:

```bash
git status --short
```

| Status | Action |
|--------|--------|
| Clean working tree | ✅ Proceed |
| Uncommitted changes on current branch | ⚠️ Ask user: stash, commit, or abort |
| On `dev` or `main` with changes | 🚫 STOP — must resolve before proceeding |

### 4. Update Dev Branch

For EACH repo:

// turbo
```bash
git checkout dev
git pull origin dev
```

If pull fails (conflicts, network), report error and ask user.

### 5. Create or Checkout Branch

For EACH repo, apply the SAME branch name:

**Check if branch exists:**
```bash
git branch -a | grep -i "{BRANCH_NAME}"
```

**Branch exists (local or remote):**
```bash
git checkout {BRANCH_NAME}
git pull origin {BRANCH_NAME} 2>/dev/null || true
```

**Branch does NOT exist:**
```bash
git checkout -b {BRANCH_NAME}
```

Do NOT push new branches until actual work is committed. User controls when to push.

### 6. Update Topic README

// turbo
If branch name wasn't already in `docs/{TOPIC_SLUG}/README.md`, update it:
```markdown
**Branch:** `{BRANCH_NAME}`
```

### 7. Verify and Report

Verify:
- [ ] Both repos on `{BRANCH_NAME}`
- [ ] Working trees are clean
- [ ] Topic docs exist and are readable
- [ ] PLAN.md has identifiable next phase

```
✅ Ready to work on: {TOPIC_SLUG}

📁 Topic: docs/{TOPIC_SLUG}/
🔀 Branch: {BRANCH_NAME}
🎫 Linear: {LINEAR_ISSUE_ID}

🔧 Git Status:
- FE: on {BRANCH_NAME} ✅ (clean)
- BE: on {BRANCH_NAME} ✅ (clean)

📋 Plan Phases:
{List phases from PLAN.md with status emoji}

🎯 Starting Phase: {First incomplete phase}
   First task: {First unchecked task}

🎯 Next: /kickoff to begin work
```

---

## Error Handling

| Error | Action |
|-------|--------|
| Topic folder missing | Suggest `/topic-new` first |
| Uncommitted changes | Ask user: stash / commit / abort |
| Branch name conflict | List existing branches, ask user to choose |
| Git pull fails | Report error, suggest manual resolution |
| Linear MCP unavailable | Use manual branch name generation |

## Notes

- Always prepare BOTH repos even if you only expect changes in one
- Do NOT start implementation — use `/kickoff` after this workflow
- Do NOT push new branches until there's work to push
- Base branch is always `dev` (never `main`)
