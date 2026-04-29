---
trigger: always_on
---

# Fixing Bugs

- When you find a bug **inside the scope of the current task**, fix it.
- When you find a bug **outside the scope of the current task**, do NOT silently fix it. Inform the user, describe what's broken, and propose a fix — but leave the decision to expand scope to the user.
- While fixing a bug, if you do not manage to fix it within **3 iterations**, STOP work, report:
  - What you have done.
  - What the problem is.
  - What you tried.
  - What options remain (with trade-offs).
- Prefer a **minimal upstream fix** over a downstream workaround. Address the root cause, not the symptom.
- Add a regression test (or update an existing one) when the bug touches code paths covered by tests. Keep the implementation minimal — tests are the place for the rigor.
