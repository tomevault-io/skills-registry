---
name: ampdo
description: Searches for AMPDO comments in the codebase to gather feedback and execute requested changes. Use when this capability is needed.
metadata:
  author: ampcode
---

# Ampdo

Search for AMPDO: comments in the codebase to gather feedback and instructions about code changes.

## When to Use

- When reviewing feedback left in the codebase as AMPDO comments
- When looking for inline instructions or change requests
- When processing developer notes embedded in code

## Search Process

Use ripgrep to find AMPDO: comments with context:

```bash
rg "AMPDO:" -C 3
```

## Review Process

- Read each AMPDO comment and surrounding code context
- Take appropriate action based on the feedback: implement requested changes, address issues, or follow instructions
- Present findings organized by file and comment type
- Execute any action items or specific change requests

## Output Format

- Group by file path
- Show line numbers and full context for each AMPDO comment
- Summarize key themes and action items at the end

## Expected Actions

After finding AMPDO: comments:

1. Analyze the feedback or instructions in each comment
2. Implement any requested code changes
3. Address any issues or concerns raised
4. Remove or update AMPDO: comments once addressed
5. Provide a summary of all actions taken

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ampcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
