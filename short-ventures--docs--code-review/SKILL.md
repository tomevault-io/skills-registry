---
name: code-review
description: Guidance on how to conduct a review of your work, always to be called upon task completion Use when this capability is needed.
metadata:
  author: short-ventures
---

Once you have completed your work, you are now to conduct a full, exhaustive review of your implementation and its possibly related files.

You need to audit for the following:

1. Old code that your changes have now made redundant
    - Look at code you have altered and files that depdend on it
2. Possible bugs based on your review
    - Look at related files your changes may impact unexpectedly
3. Inefficient/duplicate code that can be consolidated
    - Run checks to ensure you are not duplicating pre-existing patterns
    - Use services and components to consolidate/extend patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/short-ventures) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
