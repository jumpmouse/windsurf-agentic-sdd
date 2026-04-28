---
description: Create a Pull Request for completed topic work
---

# TOPIC PR

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

Create a GitHub Pull Request for completed work on a topic. Runs pre-flight checks, gathers context from documentation, and generates a well-structured PR.

**Protocol: PRs always target `dev`. Both repos checked. All work must be committed.**

## Inputs

- **TOPIC_SLUG** (optional): Topic folder name (auto-detect from current branch if not provided)

## Repo Paths & GitHub URLs

| Repo | Path | GitHub |
|------|------|--------|
| FE | `/Users/milosbr/projects/lot/fe/landOfTales` | `landoftales/LOT_FE` |
| BE | `/Users/milosbr/projects/lot/be` | `landoftales/LOT_BE` |

## Steps

### 1. Gather Context

// turbo
Read topic documentation:
1. `docs/{TOPIC_SLUG}/README.md` — Linear issue IDs, title, description
2. `docs/{TOPIC_SLUG}/CHANGELOG.md` — full list of all changes
3. `docs/{TOPIC_SLUG}/PLAN.md` — verify phase completion status
4. All `docs/{TOPIC_SLUG}/SESSION-CHANGES-*.md` — files modified across sessions

Extract:
- **PRIMARY_LINEAR_ID**: Main LAN-XXX from README
- **ALL_LINEAR_IDS**: Every LAN-XXX referenced
- **TOPIC_TITLE**: From README heading
- **BRANCH_NAME**: From README or git

### 2. Pre-Flight Checks

For EACH repo (FE and BE):

// turbo
```bash
git branch --show-current
git status --short
git log origin/dev..HEAD --oneline
```

**Pre-flight checklist:**

| Check | Requirement | Action if fails |
|-------|-------------|-----------------|
| Correct branch | NOT on `dev` or `main` | 🚫 STOP — wrong branch |
| Clean working tree | `git status` shows nothing | ⚠️ Ask user to commit or stash |
| Has commits | `git log origin/dev..HEAD` is non-empty | ⚠️ Skip this repo (no changes) |
| Branch pushed | Remote tracking exists | Push in Step 3 |

**Determine which repos need PRs:**
- FE has commits ahead of dev → needs FE PR
- BE has commits ahead of dev → needs BE PR
- Neither → report "nothing to PR" and stop

### 3. Push Branches

For each repo with unpushed commits:

```bash
git push -u origin {BRANCH_NAME}
```

If push fails, report error and ask user.

### 4. Build PR Content

**PR Title** (exact format):
```
[{PRIMARY_LINEAR_ID}] {Topic Title}
```

**PR Body** (exact template):
```markdown
## Summary

{2-3 sentence description of what was implemented, from CHANGELOG.md}

## Linear Issues

{For each LAN-XXX:}
- [{LAN-XXX}](https://linear.app/land-of-tales/issue/{LAN-XXX}) — {issue title}

## Changes

{Bullet list from CHANGELOG.md, grouped by phase:}

### Phase {N} — {Name}
- {Change 1}
- {Change 2}

## Files Modified

### Backend
- `{path}` — {what changed}

### Frontend
- `{path}` — {what changed}

## Testing

- ✅ Local build passes (`nx build` / `dotnet build`)
- ✅ No TypeScript/C# errors
- {Specific testing notes from topic context}

## Documentation

- Topic docs: `docs/{TOPIC_SLUG}/`
- [PLAN.md](https://github.com/landoftales/landoftales.github.io/blob/master/{TOPIC_SLUG}/PLAN.md)
- [CHANGELOG.md](https://github.com/landoftales/landoftales.github.io/blob/master/{TOPIC_SLUG}/CHANGELOG.md)
```

### 5. Create Pull Request

For EACH repo that has changes, use **GitHub MCP** to create the PR:

```
mcp1_create_pull_request(
  owner: "landoftales",
  repo: "LOT_BE" or "LOT_FE",
  title: "[{PRIMARY_LINEAR_ID}] {Topic Title}",
  head: "{BRANCH_NAME}",
  base: "dev",
  body: "{PR body from Step 4}"
)
```

**Settings:**
- Base branch: **`dev`** (NEVER `main`)
- Head branch: `{BRANCH_NAME}`

**If GitHub MCP unavailable**, generate compare URLs for manual creation:
```
FE: https://github.com/landoftales/LOT_FE/compare/dev...{BRANCH_NAME}
BE: https://github.com/landoftales/LOT_BE/compare/dev...{BRANCH_NAME}
```

### 6. Update Documentation

// turbo
After PR is created:

**CHANGELOG.md** — append:
```markdown
### Pull Request
- FE PR: #{PR_NUMBER} ({URL})
- BE PR: #{PR_NUMBER} ({URL})
```

**PLAN.md** — update topic status:
```markdown
## Topic Status: 🔄 PR Created
```

**README.md** — update status:
```markdown
- **Topic Status:** 🔄 PR Created
```

### 7. Report

```
✅ Pull Request Ready

🔗 PR URLs:
- FE: {GitHub compare URL or PR URL}
- BE: {GitHub compare URL or PR URL}

📝 Title: [{PRIMARY_LINEAR_ID}] {Topic Title}
🎫 Linear Issues: {ALL_LINEAR_IDS}
🔀 Branch: {BRANCH_NAME} → dev

📋 Next Steps:
1. Review PR content in GitHub
2. Request code review if needed
3. Merge when approved
4. Delete branch after merge
5. /milestone-close when milestone complete
```

---

## Error Handling

| Error | Action |
|-------|--------|
| Wrong branch | STOP, report, ask user to switch |
| Uncommitted changes | Ask user to commit via `/phase-x-finalize` |
| No commits ahead of dev | Skip that repo, report |
| Push fails | Report error, suggest manual push |
| PLAN.md shows incomplete phases | Warn user, ask if they want to proceed |

## Notes

- PRs ALWAYS target `dev` branch
- Both repos get PRs if both have changes
- If only one repo has changes, only create PR for that repo
- PR body is generated from docs, not improvised
- This workflow does NOT merge — user controls merge timing
