---
name: win-mouse-native
description: Native Windows mouse control (move, click, drag) via user32.dll. Use when the user asks you to move the mouse, click, drag, or automate pointer actions on Windows. Use when this capability is needed.
metadata:
  author: openclaw
---

# Win Mouse Native

Provide deterministic mouse control on **Windows**.

## Commands (local)

This ClawHub bundle is **docs + scripts-as-text** (ClawHub validates “text files only”).

To install:
1) Save `win-mouse.cmd.txt` as `win-mouse.cmd`
2) Save `scripts/win-mouse.ps1.txt` as `scripts/win-mouse.ps1`

Then run:
- `win-mouse move <dx> <dy>` (relative)
- `win-mouse abs <x> <y>` (absolute screen coords)
- `win-mouse click left|right|middle`
- `win-mouse down left|right|middle`
- `win-mouse up left|right|middle`

Return value: prints a one-line JSON object.

## OpenClaw usage

When the user asks to move/click the mouse:

1) If the user didn’t give coordinates/deltas, ask.
2) Use `exec` to run `win-mouse ...`.
3) Prefer small, reversible actions first (tiny move, single click) when unsure.

## Notes

- Windows only.
- Uses Win32 `SetCursorPos` + `SendInput` via user32.dll.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
