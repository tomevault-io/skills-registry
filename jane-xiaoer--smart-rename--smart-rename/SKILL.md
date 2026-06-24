---
name: smart-rename
description: Content-aware file renamer for macOS. Right-click any image / video / PDF / Word / markdown / folder → AI generates a meaningful Chinese filename. Use when user says "改名 / 重命名 / 整理文件夹 / 给文件起名". This skill assumes Smart Rename is installed (see https://github.com/<owner>/smart-rename). Use when this capability is needed.
metadata:
  author: Jane-xiaoer
---

# Smart Rename — Claude Skill

Smart Rename is a local CLI that uses Google Gemini Vision/Text to generate
meaningful filenames. Files are renamed to `<content-words>_<YYYY-MM-DD>.<ext>`.

**The renaming logic runs locally — Claude never sees the file content.**
Claude only orchestrates: ask the user which paths, call the CLI, report back.

## When to use

- "把 ~/Downloads 的图片都改个名字"
- "整理这个文件夹的 PDF"
- "给这堆视频起个有意义的名字"
- "重命名 / 改名 / rename"

## Prerequisites

- macOS only
- `~/.local/bin/smart-rename` exists (run `which smart-rename`)
- API key configured (run `smart-rename --doctor` to verify)

If not installed, point user to the install script:
```bash
git clone https://github.com/<owner>/smart-rename.git
cd smart-rename && ./install.sh
smart-rename --setup
```

## How to drive it

Claude should call the CLI directly via the Bash tool. **Do not implement
renaming logic inside Claude** — that wastes tokens.

### Step 1 — Preview

```bash
smart-rename --preview <path1> <path2> ... > /tmp/sr-plan.json
```

The CLI returns JSON like:
```json
[
  {"old": "/Users/x/IMG_001.jpg", "new": "/Users/x/京都岚山_2026-04-25.jpg", "status": "ok"},
  {"old": "/Users/x/bad.xyz", "new": null, "status": "error", "error": "..."}
]
```

### Step 2 — Show plan to user

Read the JSON and display each rename in a friendly format:
```
京都岚山_2026-04-25.jpg  ← IMG_001.jpg
```

Ask the user to confirm.

### Step 3 — Apply

```bash
smart-rename --apply /tmp/sr-plan.json
```

Prints a log path. Files are renamed atomically.

### Step 4 — If user wants to undo

```bash
smart-rename --undo
```

Restores the most recent rename batch.

## Supported file types

| Type | Extensions |
|------|------------|
| Image | jpg, jpeg, png, heic, webp, gif |
| Video | mp4, mov, m4v, mkv, avi, webm |
| Document | pdf, docx, doc, rtf, odt |
| Text | md, markdown, txt, csv, json, xml, html |
| Folder | (any directory) |

## Two parallel trigger paths

Users can also trigger rename without Claude:

1. **Right-click in Finder** → Quick Actions → "改名 (Smart Rename)"
2. **⌥⌘R** (if Hammerspoon module installed)

These use the same CLI under the hood. Claude is just one of several
front-ends.

## Don't

- Don't read the file content yourself and try to name it. The CLI does
  this with Gemini Flash, which is far cheaper than Claude.
- Don't suggest names without running `--preview` first.
- Don't apply without showing the user the preview.

---
> Source: [Jane-xiaoer/smart-rename](https://github.com/Jane-xiaoer/smart-rename) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
