---
name: uloop-focus-window
description: Bring Unity Editor window to front via uloop CLI. Use when you need to: (1) Focus Unity Editor before capturing screenshots, (2) Ensure Unity window is visible for visual checks, (3) Bring Unity to foreground for user interaction. Use when this capability is needed.
metadata:
  author: hatayama
---

# uloop focus-window

Bring Unity Editor window to front using OS-level commands.

## Usage

```bash
uloop focus-window
```

## Parameters

None.

## Global Options

| Option | Description |
|--------|-------------|
| `--project-path <path>` | Target a specific Unity project. Path resolution follows the same rules as `cd` — absolute paths are used as-is, relative paths are resolved from cwd. |

## Examples

```bash
# Focus Unity Editor
uloop focus-window
```

## Output

Returns JSON confirming the window was focused.

## Notes

- **Works even when Unity is busy** (compiling, domain reload, etc.)
- Uses OS-level commands (osascript on macOS, PowerShell on Windows)
- Useful before `uloop capture-unity-window` to ensure the target window is visible
- Brings the main Unity Editor window to the foreground

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hatayama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
