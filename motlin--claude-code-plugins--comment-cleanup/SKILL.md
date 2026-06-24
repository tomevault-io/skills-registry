---
name: comment-cleanup
description: Guidelines for comments in code. Use when adding, editing, or removing comments. Use when this capability is needed.
metadata:
  author: motlin
---

# Comment Guidelines

- Use comments sparingly
- Don't comment out code
    - Remove it instead
- Don't add comments that describe the process of changing code
    - Comments should not include past tense verbs like added, removed, or changed
    - Example: `this.timeout(10_000); // Increase timeout for API calls`
    - This is bad because a reader doesn't know what the timeout was increased from, and doesn't care about the old behavior
- Don't add comments that emphasize different versions of the code, like "this code now handles"
- Do not use end-of-line comments
    - Place comments above the code they describe

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/motlin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
