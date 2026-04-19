---
name: capture-screen-windows
description: Captures screenshots on Windows systems. Can take full screen screenshots or capture specific application windows (e.g., Chrome, Edge, Firefox, Visual Studio Code). Use when the user needs to capture screenshots on Windows, list available windows, or get information about the currently active window. Use when this capability is needed.
metadata:
  author: langqi99
---

# Screen Capture Skill (Windows)

## Capabilities
- Take full screen screenshots.
- Take screenshots of specific application windows (e.g., Browsers like Chrome, Edge, Firefox).
- Get information about the currently active window or list all windows.

## Best Practices

⚠️ **IMPORTANT**: Before taking a window-specific screenshot, **ALWAYS** list all available windows first to identify the correct window name or title. Window class names and titles can vary, so listing helps ensure you use the correct identifier.

**Recommended Workflow:**
1. Run `python capture-screen-windows/window_info.py list` to see all available windows
2. Identify the window by its title or class name from the JSON output
3. Use that title or class name in the screenshot command

## Usage

### 1. Get Window Information (CRITICAL FIRST STEP)

⭐ **ALWAYS START HERE** when capturing specific windows to avoid errors.

**List All Windows (Recommended):**
Returns a JSON array with all detected windows, including class name, window title, ID, bounds, and area.

```bash
python capture-screen-windows/window_info.py list
```

Example output:
```json
[
  {
    "app": "Chrome_WidgetWin_1",
    "title": "GitHub - example/repo - Google Chrome",
    "id": 12345,
    "bounds": {"X": 0, "Y": 0, "Width": 1920, "Height": 1080},
    "area": 2073600
  },
  {
    "app": "CabinetWClass",
    "title": "Documents",
    "id": 67890,
    "bounds": {"X": 100, "Y": 100, "Width": 800, "Height": 600},
    "area": 480000
  }
]
```

**Get Active Window Info:**
Returns JSON with info about the currently active (frontmost) window.

```bash
python capture-screen-windows/window_info.py active
```

### 2. Take Screenshots

#### Full Screen Screenshot
Run the python script with the `full` command:
```bash
python capture-screen-windows/screenshot.py full <output_path>
```

Example:
```bash
python capture-screen-windows/screenshot.py full C:\temp\my_screen.png
```

#### Window Screenshot
Run the python script with the `window` command, specifying the window title or class name and output path.

⚠️ **Important**: Use the window title or class name from `window_info.py list` output. The script supports fuzzy matching (searches in both title and class name).

```bash
python capture-screen-windows/screenshot.py window "<Window Title or Class>" <output_path>
```

Examples:
```bash
python capture-screen-windows/screenshot.py window "Chrome" C:\temp\chrome_window.png
python capture-screen-windows/screenshot.py window "Visual Studio Code" C:\temp\vscode_window.png
python capture-screen-windows/screenshot.py window "Command Prompt" C:\temp\cmd_window.png
```

#### List Windows (Human Readable)
To see a formatted table of windows for quick reference:
```bash
python capture-screen-windows/screenshot.py list
```

## Dependencies & Requirements
- Windows OS
- Python 3
- Required packages:
  - `pywin32` - For Windows API access
  - `pillow` - For image capture and manipulation

### Installation
Install dependencies using pip:
```bash
pip install pywin32 pillow
```

## Technical Details

### Window Identification
- Uses Win32 API (`win32gui`) to enumerate windows
- Captures window class names and titles
- Filters out invisible windows
- Sorts by area to prioritize main windows over dialogs

### Screenshot Method
- Full screen: Uses `PIL.ImageGrab.grab()`
- Window capture: Uses `PIL.ImageGrab.grab(bbox=...)` with window coordinates
- Brings target window to foreground before capture
- Outputs high-quality PNG images

## Troubleshooting

### Permission Issues
Some windows (especially system windows) may require elevated permissions. Run your terminal as Administrator if you encounter permission errors.

### Import Errors
If you see `ImportError: No module named 'win32gui'`, install pywin32:
```bash
pip install pywin32
```

After installation, you may need to run:
```bash
python -m pywin32_postinstall -install
```

### Window Not Found
If a window isn't being captured:
1. Ensure the window is visible (not minimized)
2. Check the exact title using the `list` command
3. Try using a partial match from the title
4. Try using the window class name instead

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/langqi99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
