# {Project name in plain English} — Update for {Audience}

[← Back to README](../README.md)

> 📅 **Reporting period:** {Human-friendly period, e.g. "April 16 – April 28, 2026"}
> 👥 **For:** {Audience description, e.g. "non-technical readers / partners / friends-and-family"}
> ✍️ **Written:** {YYYY-MM-DD}

---

## 🎯 The 30-Second Version

> _One short paragraph in everyday language. Read this and you know the gist._
>
> {Example: "We're {high-level analogy for the work — e.g. 'upgrading the
> engine of the website while it's still running, like changing the tires on
> a car going down the highway'}. This {period} we finished {what}. Everything
> still {what users care about} — that's the goal. Up next: {next step in
> plain language}."_

---

## 🧭 What We Did This Period

> _Bullet list. Plain English. Each bullet starts with an action verb. No jargon._
> _If you must use a technical term, put it in **bold** and define it in the
> Glossary at the bottom._

- {Plain-English bullet 1 — use a relatable analogy if the underlying work is technical}
- {Plain-English bullet 2 — focus on outcome / impact, not mechanism}
- {Plain-English bullet 3 — highlight any safety net you put in place (test, automation, documentation)}
- {Plain-English bullet 4 — mention groundwork that unblocks future work}

---

## 🌟 What This Means for You

> _Translate the work into "why should the reader care". This is the most
> important section for non-technical audiences._

- **For users of the {product}:** {what they will / won't notice}
- **For the business:** {plain-language outcome — risk reduced, capability unlocked, next step enabled}
- **For other team members:** {plain-language internal benefit — cleaner code, faster fixes, better docs}

---

## 🛣️ The Journey So Far

> _A simple visual that shows where we are along the path. Use a progress bar
> with filled and empty blocks, plus a Mermaid timeline if helpful._

**Overall progress:** `▰▰▱▱▱▱▱` **2 of 7 phases complete** _(roughly 28%)_

```mermaid
%% Replace with the actual phase list for your topic. Keep it simple —
%% partners often read this on phones.
gantt
    title Project journey — start to finish
    dateFormat  YYYY-MM-DD
    axisFormat  %b
    section Done
    {Phase 0 — plain-English title}    :done,    p0, {start}, {end}
    {Phase 1 — plain-English title}    :done,    p1, {start}, {end}
    section Next
    {Phase 2 — plain-English title}    :         p2, {start}, {end}
    section Later
    {Phase 3}                          :         p3, {start}, {end}
    {Phase 4}                          :         p4, {start}, {end}
    {Phase 5}                          :         p5, {start}, {end}
    {Phase 6}                          :         p6, {start}, {end}
```

> _Tip: keep this Mermaid diagram simple — partners read it on phones too._

---

## 📊 By the Numbers

> _Translate raw metrics into something a 15-year-old can picture. Use
> comparisons, not jargon._

| What | Number | Plain-English meaning |
|------|-------:|------------------------|
| {e.g. Files updated}              | {N}      | {everyday-language meaning, with an analogy if useful} |
| {e.g. Tests passing}              | {X / Y}  | {what "every test passing" means in practice} |
| {e.g. Visible bugs introduced}    | {0}      | {plain-language interpretation of zero impact} |
| {e.g. Build time / load time}     | {value}  | {analogy: "faster than X" / "about as long as Y"} |

---

## 🚀 What's Coming Next

> _Plain-language preview of the next step or two. Avoid technical detail._

**Next up: {Title of the next ticket / phase, in plain English}**

- {Plain-English description of what the next step does}
- {Why it matters in plain language}
- {Approximate timing if known, otherwise "starting next week"}

**After that:** {Brief gesture at the step beyond next.}

---

## 📖 Glossary _(optional — only if you used unavoidable jargon above)_

> _Each entry: term in bold, then a sentence anyone could understand._

- **{Term 1}** — plain-English definition with an analogy. Aim for one a
  non-technical reader could repeat to a friend.
- **{Term 2}** — same.
- **{Term 3}** — same.

{Worked examples — "Bootstrap", "jQuery", "Build" — are preserved in
`.windsurf/_templates/examples/stakeholder-report__sxe-bootstrap.md` for
reference.}

---

## 💬 Questions?

> _Always end with an invitation. Non-technical readers shouldn't feel they
> can't ask "dumb" questions._

If something here didn't make sense, please ask — there are no silly
questions.

---

> 🔗 **For the technically curious:** the engineering details for this period
> live in [`{topic}/CHANGELOG.md`](./CHANGELOG.md) and [`{topic}/WORK_LOG.md`](../WORK_LOG.md).
> _(Linked for reference — you don't need to read them.)_
