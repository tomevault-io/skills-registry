---
name: discussion-commenter
description: Post Q&A entries from office hours writeups as comments to a GitHub Discussion. Use when the user wants to post writeup content to a discussion thread. Use when this capability is needed.
metadata:
  author: pamelafox
---

# Discussion Commenter

Post Q&A entries from office hours writeups as individual comments to a GitHub Discussion.

## Usage

Run the script with the writeup file path, discussion URL, and date:

```bash
uv run .github/skills/discussion-commenter/post_qas.py \
  "path/to/writeup.md" \
  "https://github.com/orgs/ORG/discussions/NUMBER" \
  "2026/01/06"
```

## Arguments

1. `writeup_path` - Path to the markdown writeup file containing Q&As
2. `discussion_url` - Full URL to the GitHub discussion
3. `date` - Date string to prefix each comment (e.g., "2026/01/06")

## Options

- `--dry-run` - Parse and display what would be posted without actually posting

## Requirements

- GitHub CLI (`gh`) must be installed and authenticated
- User must have permission to comment on the discussion

## Output

Each `## Question` section in the writeup becomes a separate comment, formatted as:

```markdown
**2026/01/06: Question Title**

Answer content...
```

Subsections (`### Follow-up`) are included in the parent question's comment.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pamelafox) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
