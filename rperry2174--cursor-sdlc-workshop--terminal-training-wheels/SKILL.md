---
name: terminal-training-wheels
description: Adds a quick, beginner-friendly “how to run this / what command to type / what to look for” appendix after responses that include terminal commands, local setup, running apps, installing dependencies, or debugging command errors. Use when this capability is needed.
metadata:
  author: rperry2174
---

# Terminal Training Wheels (Beginner Appendix)

## When to use

Use this when the response includes (or implies) terminal usage, such as:
- installing tools (brew, npm, node, python, pip, etc.)
- running the app (dev server, build, tests)
- Git/GitHub commands
- “command not found” / PATH problems
- “where do I run this?” questions

## Core behavior

After answering the main question, append a short “training wheels” appendix that assumes the learner:
- doesn’t know where the terminal is
- doesn’t know what folder they’re “in”
- doesn’t know what success looks like

Keep it short and actionable:
- 4–10 bullets total
- use the exact commands already mentioned (don’t add a big new workflow)
- explain what output indicates success
- include 1–2 common failure fixes when likely (wrong folder, missing install, permission)

## Output template (append at end)

### Quick terminal guide (beginner)
- **Open a terminal**: Use Cursor’s terminal panel or your system Terminal app.
- **Go to the right folder**: `cd /path/to/project` (copy/paste the path).
- **Confirm you’re in the right place**: `pwd` (shows current folder).
- **Run the command**: paste the command exactly as shown.
- **What “worked” looks like**: mention the success signal (e.g., “no errors”, “server started”, “Created commit …”).
- **If you see an error**:
  - **`command not found`**: the tool isn’t installed or PATH isn’t set.
  - **wrong folder**: `ls` and look for `package.json` / `index.html` / expected files.

## Examples (short)

If the answer includes: “Run `npm install` then `npm run dev`”

Append:

### Quick terminal guide (beginner)
- **Go to the project folder**: `cd /Users/you/.../slides-react`
- **Install once**: `npm install` (success = it finishes without red errors)
- **Start it**: `npm run dev` (success = it prints a local URL like `http://localhost:5173`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rperry2174) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
