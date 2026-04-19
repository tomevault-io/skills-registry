---
name: add-lc
description: Save LeetCode session summary to Obsidian. Use after completing a LeetCode practice session to capture your solution, bugs fixed, and key learnings. Use when this capability is needed.
metadata:
  author: manavarora506
---

# Add LeetCode Progress Note

Save a summary of the current LeetCode practice session to Obsidian.

## Instructions

1. **Extract from the conversation:**
   - Problem name and number (ask if not clear)
   - Final working code
   - Initial bugs/issues that were identified and fixed
   - Key concepts discussed and clarified
   - Important nuances learned
   - Any notable Q&A exchanges

2. **Create the problem note** using `mcp__obsidian__obsidian_append_content`:
   - Filepath: `3. Resources/LeetCode/{problem-number}-{problem-name-slug}.md`
   - Example: `3. Resources/LeetCode/752-open-lock.md`

3. **Note format:**

```markdown
# {Problem Number}. {Problem Name}

**Date:** {YYYY-MM-DD}
**Difficulty:** {Easy/Medium/Hard}
**Topics:** {BFS, DFS, DP, etc.}
**Link:** https://leetcode.com/problems/{slug}/

## Final Solution

\`\`\`python
{final working code}
\`\`\`

## Initial Issues

{List bugs/mistakes from first attempt}

## Key Learnings

{Concepts that were clarified during the session}

## Nuances to Remember

{Specific details, edge cases, or gotchas}

## Q&A Highlights

{Notable questions and insights from the discussion}
```

4. **Update the index** using `mcp__obsidian__obsidian_append_content`:
   - Filepath: `3. Resources/LeetCode/index.md`
   - Append: `- [[{problem-number}-{problem-name-slug}]] - {one-line summary}`

5. **Confirm** to the user that the note was saved and provide the filepath.

## Tools Required

- `mcp__obsidian__obsidian_append_content` - to create/update notes
- `mcp__obsidian__obsidian_get_file_contents` - to check if index exists

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manavarora506) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
