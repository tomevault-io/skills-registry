---
name: debug-report
description: Systematic approach to diagnosing failing tests — trace the root cause, add instrumentation, and escalate clearly when stuck. Use when this capability is needed.
metadata:
  author: ayazhankadessova
---

# Debugging Failing Tests

When a test fails, follow this sequence before giving up:

1. **Understand the test** — read its name and body to know exactly which behaviour it verifies.
2. **Walk the call stack** — trace backwards from the failure to find the function responsible.
3. **Verify inputs** — confirm the failing function receives the values you expect.
4. **Add instrumentation** — insert temporary `print` / `console.log` / `dbg!` statements to:
   - Pinpoint the branch or condition that diverges from the expected path.
   - Identify the exact variable and the moment its value departs from what is correct.
5. **Escalate if stuck** — if the above steps don't lead to a fix, stop and present a structured report to the user containing:
   - Test name
   - Full error message and stack trace
   - Inputs passed to the failing function
   - Observed value vs. expected value
   - Any additional observations
6. **Link to an issue when appropriate** — if the failure relates to work tracked in a GitHub issue (check the branch name for a clue), post the report as a comment on that issue. Otherwise, display it directly.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ayazhankadessova) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
