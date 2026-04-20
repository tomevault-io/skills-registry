---
name: summarize-work
description: Based on the context, write a summary of changes, what's left, production readiness, tests run, and APIs/packages used. Use when this capability is needed.
metadata:
  author: inline-chat
---

Write a summary of work you've done in this session.

# High-level instructions and output format

- Review the files you've changed
- Write a list of added packages/new APIs you have used in a bullet list for a developer review and reason you used them. If there is an alternative provide a concise clause.
- Write a list of high-level product/UI/UX/features/fixes/changes. What user will see/experience in a bullet list. Think of this as a spec list.
- Write a section on what tests you've run, the confidence level for shipping this to production, and if anything is left from the original intent or work.
- Write a section on top most important 1-5 files/functions/areas for the senior developer to review. Keep this to essentials only. Include short snippets of core areas for a quick evaluation too.
- Include a list of changed files and number of lines changed/remove in another section at the bottom.

# Tips
- From first section to last, go from high level/concise to deeper insights.
- If asked to do it "fast", skip re-reading files and diffs, and rely on your context.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/inline-chat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
