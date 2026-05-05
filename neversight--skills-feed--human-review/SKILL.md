---
name: human-review
description: Enable AI agents to collect human code review feedback via web interface and apply suggested changes Use when this capability is needed.
metadata:
  author: neversight
---

## Human Review Skill

Automatically launches a web-based review interface, waits for user to complete comments, then applies suggested changes.

**Dependency**: This skill requires the `hrevu` CLI tool. Install first:
```bash
cargo install human-review
```

## Workflow

### 1. Detect Changes and Launch hrevu

Determine current state:
- User specified file → `hrevu <file>`
- User specified commit → `hrevu <commit>`
- No argument → Check git changes, use `hrevu diff` if changes exist

### 2. Wait for Review Completion

- Run hrevu command
- Browser automatically opens review interface
- **Wait** for user to complete comments in browser
- After user clicks "Finish Review", hrevu outputs summary and exits

### 3. Parse Review Results

Parse review results from terminal output. Each comment contains:
- File name
- Line number
- Comment content
- Source code context (marked with `▸`)

### 4. Automatically Apply Changes

Apply modifications using Edit tool based on comments.

**Skip Strategy:**
- Pure praise (e.g., "Great!", "LGTM")
- Vague suggestions without specific modification plans

**Modification Strategy:**
- Explicit code changes → Use Edit directly
- Variable/function renaming → Use Edit + replace_all
- Add content (error handling, imports, etc.) → Add corresponding code

**Important:**
- **Must wait** for hrevu to complete before continuing
- Use Edit tool to modify code, preserve original indentation and formatting
- Report location and content after each modification
- Confirm with user if uncertain about any change

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
