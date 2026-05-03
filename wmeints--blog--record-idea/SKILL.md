---
name: record-idea
description: Use this skill to a new idea for a blogpost or to update an existing idea for a blogpost. Make sure to include a detailed description for the blogpost.
metadata:
  author: wmeints
---

Use this skill to a new idea for a blogpost or to update an existing idea for a blogpost. Make sure to include a detailed description for the blogpost.

## Instructions

When the user provides a blog idea, do the following:

1. **Check for duplicates first.** Read all existing files in the `ideas/`
directory and compare their titles and descriptions against the new idea. If an
existing idea covers the same or a very similar topic, update that file instead
of creating a new one. Merge any new details (key points, references, notes)
into the existing file rather than overwriting them.

2. Ask the user for any details they haven't provided yet. At minimum you need:
   - A short title for the idea
   - A brief description of what the post would cover

   The user may also optionally provide:
   - Target audience notes
   - Key points or angles to explore
   - Relevant links or references
   - Suggested post structure (see `.claude/agents/redaction.md` for available structures)

3. Generate a URL-friendly slug from the title (lowercase, hyphens, no special characters).

4. Create or update a markdown file at `ideas/<slug>.md` with this format:

   ```markdown
   # <Title>

   ## Description

   <Brief description of what the post would cover>

   ## Key Points

   - <Point 1>
   - <Point 2>
   - ...

   ## Notes

   <Include the original idea description here or expand this section with additional notes>

   ## References

   - <Any relevant links or resources>
   ```

5. Omit any optional sections the user didn't provide information for.

6. Confirm to the user whether the idea was **newly recorded** or **merged into an existing idea**, and show the file path.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wmeints) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
