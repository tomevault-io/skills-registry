---
name: file-editor
description: description: "Reads, writes, creates, updates, and deletes frontend and backend source files based on specs and Claude instructions." Use when this capability is needed.
metadata:
  author: shuremali02
---
---
name: "file-editor"
description: "Reads, writes, creates, updates, and deletes frontend and backend source files based on specs and Claude instructions."
version: "1.0.0"
---

# File Editor Skill

## When to Use This Skill

- Claude needs to **edit or create files**
- Implementing a spec
- Refactoring code
- Creating routes, pages, or models

## How This Skill Works

1. Reads target file
2. Understands existing code
3. Applies changes from spec
4. Writes back clean updated code
5. Never breaks project structure

## Output Format

- File path
- Action: created / updated / deleted
- Code diff or full updated file

## Example

**Input:** "Update backend/main.py to add JWT middleware"

**Output:**  
`backend/main.py` → updated with JWT validation middleware

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shuremali02) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
