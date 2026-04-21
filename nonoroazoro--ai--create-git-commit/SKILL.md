---
name: create-git-commit
description: Create git commits Use when this capability is needed.
metadata:
  author: nonoroazoro
---

## Workflow

1. Check staging: Run `git status`
   - If changes are already staged: proceed using ONLY the currently staged changes, DO NOT stage additional files
   - If nothing is staged: stage only the specific files relevant to the change using `git add <file>`

2. Analyze changes: Run `git diff --cached`
   - If multiple unrelated changes detected: split into separate commits by selectively adding files

3. Learn project conventions: Run `git log --oneline -20`
   - Identify the commit message format used in the project (e.g., conventional commits, prefix style, plain style)
   - Follow the project's existing format

4. Create commit message:
   - Match the format identified in step 3
   - If no clear convention exists, fall back to CONVENTIONAL COMMIT format:

      ```text
      <type>[!]: <description>

      [optional body]
      ```

      - Type: feat, fix, docs, style, refactor, perf, test, build, chore

   - Description: <72 chars, use imperative mood
   - Body:
      - Include only if necessary and format the body line-by-line using bullet points
      - CONSTRAINT: DO NOT add any "Generated with", "Co-Authored-By", or tool attribution text in the message body

5. Handle AI-generated trailer:
   - If `$ARGUMENTS` equals "ai": add trailer via `-m "AI-Generated-By: [Coding Tool Name]"`
   - If `$ARGUMENTS` is empty or any other value: complete the commit without trailer

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nonoroazoro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
