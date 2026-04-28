# Pattern Matching Investigation Rule

## 🌐 Project Override Hook

This is a **global rule**. Once per session (or whenever this rule's content is loaded into context), check whether the host project provides an override.

**Where to look** — in the host project's workspace root (NOT in the global rules repo):

```
.windsurf/overrides/rules/<same-filename-as-this-file>.md
```

Use the same filename as this rule file's basename.

**If that override file exists**, read it and apply the first matching mode (declared in frontmatter as `mode: replace|extend|modify`, OR via a first-heading marker `# REPLACE` / `# EXTEND` / `# MODIFY`):

- **REPLACE** — IGNORE the rule below; follow ONLY the override.
- **EXTEND** — follow the rule below AND additionally follow the override's directives.
- **MODIFY** — follow the rule below, but with the specific bullets / sections the override substitutes or refines.

**If no override file exists**, follow the rule below as written. If the override's mode is missing or ambiguous, follow the global rule as-is and surface the ambiguity to the user when convenient.

---

## When This Rule Applies

When the user says any of:
- "exactly like X"
- "same as X"
- "follow the pattern of X"
- "like we did for X"
- "similar to X"
- "match X behavior"

## MANDATORY Steps (NO EXCEPTIONS)

### Step 0: Inform User About Following Rules
In thread where agent communicates with the user, print "FOLLOWING PATTERN MATHING RULE".
That way, user will be informed that you are using this set of rules.
This is very important. First do this, before anything else, without thinking. Just print and move on.

### Step 1: STOP - Do Not Write Code Yet
Before writing ANY code, you MUST investigate the reference implementation.

### Step 2: Find and Read the Reference
0. investigate through docs. there is high chance that we worked on this before, whatever it is.
1. Locate the exact file/function/component mentioned
2. Read the FULL implementation, not just a snippet
3. Identify ALL patterns used:
   - Helper functions called
   - Constants/enums used
   - Fallback chains
   - Error handling
   - Edge cases
4. Map all functionalities before work, end to end.
5. Make comparison with desired functionalities, full match.
6. Map data used in both reference and future code. end to end, in detail.
7. Map template and styles, end to end. Also, check TS file if any html/style manipulation.
8. If any services or helper methods/utils are used in reference, map them and compare with what is wanted.
9. Map also anything else, what is not in this list, but important for the type of work you are on. You decide.

### Step 3: Document the Pattern
Before implementing, explicitly state:
```
## Pattern from [Reference]:
- Uses: [list helper functions]
- FileTypes/Constants: [exact values]
- Fallback chain: [A] → [B] → [placeholder], NOT [A] → [thumbnail]
- Key differences from what I might assume: [list]
- Everithing else noted in step 2.
```

### Step 4: Implement by Following, Not Inventing
- Use the SAME helper functions
- Use the SAME constants
- Use the SAME fallback logic
- Do NOT invent new approaches
- Do NOT add "improvements" unless asked
- Do NOT assume anything, investigate and pin point in reference code.
- Do NOT propose new code easily. Check how it is impemented in reference. Check other places.

### Step 5: Verify Match
After implementation, compare line-by-line:
- Does my code use the same pattern?
- Any deviations? If yes, WHY and is it justified?

## Example Violation (What Went Wrong)

User asked: "CollectionHeader should work exactly like HeaderCarousel"

**Wrong approach (what I did):**
- Assumed FileType values (used 4,5 instead of 21,22)
- Added thumbnail fallback that doesn't exist in reference
- Wrote new code instead of following existing pattern

**Correct approach:**
1. Read `CarouselsDataMapperService.mapHeaderSlidesFromCarousel()`
2. Found: `pickHeaderImage()` → FileType.HeaderImage (21)
3. Found: `pickHeaderImageMobile()` → FileType.HeaderImageMobile (22) → fallback to HeaderImage
4. Found: Placeholder fallback ONLY, no thumbnail
5. Implement using exact same pattern

## Consequence of Violation
Breaking this rule creates bugs, inconsistencies, and wastes user's time fixing AI-generated mess.
