---
name: commit-wizard
description: AI-powered commit message generator that follows conventional commits and best practices Use when this capability is needed.
metadata:
  author: csmoove530
---

# Commit Wizard

Never write a bad commit message again! Commit Wizard analyzes your staged changes and generates clear, conventional commit messages.

## Usage

Just tell Claude:
- "Generate a commit message for my changes"
- "Write a conventional commit for this feature"
- "Help me commit these bug fixes"

## Features

- 🎯 Follows conventional commits format
- 📝 Analyzes git diff for context
- 🚀 Suggests scope and breaking changes
- ✨ Includes emojis (optional)

## Example

```
Input: Added user authentication with JWT tokens

Output:
feat(auth): implement JWT-based authentication

- Add JWT token generation and validation
- Create auth middleware for protected routes
- Include refresh token support

BREAKING CHANGE: Authentication now required for all /api/* endpoints
```

## Requirements

- Git repository
- Staged changes

## License

MIT

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/csmoove530) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
