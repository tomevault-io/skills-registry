---
name: capture-screen-macos
description: Captures screenshots on macOS systems. Can take full screen screenshots or capture specific application windows (e.g., Google Chrome, Safari, Visual Studio Code). Use when the user needs to capture screenshots on macOS, list available windows, or get information about the currently active window. Use when this capability is needed.
metadata:
  author: langqi99
---

# Screen Capture Skill

## Capabilities
- Take full screen screenshots.
- Take screenshots of specific application windows (e.g., Browsers like Google Chrome, Safari).
- Get information about the currently active window or list all windows.

## Best Practices

⚠️ **IMPORTANT**: Before taking a window-specific screenshot, **ALWAYS** list all available windows first to identify the correct window name. Window names can vary (e.g., "Google Chrome", "Chrome", localized names like "谷歌浏览器"), so listing helps ensure you use the exact name.

**Recommended Workflow:**
1. Run `python3 capture-screen-macos/window_info.py list` to see all available windows
2. Identify the exact app name from the JSON output
3. Use that exact app name in the screenshot command

## Usage

### 1. Get Window Information (CRITICAL FIRST STEP)

⭐ **ALWAYS START HERE** when capturing specific windows to avoid errors.

**List All Windows (Recommended):**
Returns a JSON array with all detected windows, including app name, window title, ID, bounds, and area.

```bash
python3 capture-screen-macos/window_info.py list
```

Example output:
```json
[
  {
    "app": "Google Chrome",
    "title": "GitHub - example/repo",
    "id": 12345,
    "bounds": {"X": 0, "Y": 25, "Width": 1920, "Height": 1080},
    "area": 2073600
  },
  {
    "app": "Visual Studio Code",
    "title": "skill.md - oai-skills",
    "id": 67890,
    "bounds": {"X": 100, "Y": 100, "Width": 1200, "Height": 800},
    "area": 960000
  }
]
```

**Get Active Window Info:**
Returns JSON with info about the currently active (frontmost) window.

```bash
python3 capture-screen-macos/window_info.py active
```

### 2. Take Screenshots

#### Full Screen Screenshot
Run the python script with the `full` command:
```bash
python3 capture-screen-macos/screenshot.py full <output_path>
```

Example:
```bash
python3 capture-screen-macos/screenshot.py full /tmp/my_screen.png
```

#### Window Screenshot
Run the python script with the `window` command, specifying the application name and output path. 

⚠️ **Important**: Use the exact app name from `window_info.py list` output for best results. The script supports fuzzy matching (e.g. "Safari" matches "Safari浏览器"), but exact matches are more reliable.

```bash
python3 capture-screen-macos/screenshot.py window "<App Name>" <output_path>
```

Examples:
```bash
python3 capture-screen-macos/screenshot.py window "Google Chrome" /tmp/chrome_window.png
python3 capture-screen-macos/screenshot.py window "Safari" /tmp/safari_window.png
python3 capture-screen-macos/screenshot.py window "Visual Studio Code" /tmp/vscode_window.png
```

#### List Windows (Human Readable)
To see a formatted table of windows for quick reference:
```bash
python3 capture-screen-macos/screenshot.py list
```

## Dependencies & Requirements
- macOS.
- Python 3.
- `swift` (installed by default on macOS) for low-level window queries.
- Permissions to control "System Events" (for active app detection).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/langqi99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
