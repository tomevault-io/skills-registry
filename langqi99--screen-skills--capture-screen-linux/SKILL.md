---
name: capture-screen-linux
description: Captures screenshots on Linux systems. Can take full screen screenshots or capture specific application windows (e.g., Firefox, Chrome, Terminal). Use when the user needs to capture screenshots on Linux, list available windows, or get information about the currently active window. Use when this capability is needed.
metadata:
  author: langqi99
---

# Screen Capture Skill (Linux)

## Capabilities
- Take full screen screenshots.
- Take screenshots of specific application windows (e.g., Browsers like Firefox, Chrome, Browsers).
- Get information about the currently active window or list all windows.

## Best Practices

⚠️ **IMPORTANT**: Before taking a window-specific screenshot, **ALWAYS** list all available windows first to identify the correct window name. Window class names and titles can vary across different desktop environments and applications.

**Recommended Workflow:**
1. Run `python3 capture-screen-linux/window_info.py list` to see all available windows
2. Identify the window by its app name or title from the JSON output
3. Use that app name or title in the screenshot command

## Usage

### 1. Get Window Information (CRITICAL FIRST STEP)

⭐ **ALWAYS START HERE** when capturing specific windows to avoid errors.

**List All Windows (Recommended):**
Returns a JSON array with all detected windows, including app name (WM_CLASS), window title, ID, bounds, and area.

```bash
python3 capture-screen-linux/window_info.py list
```

Example output:
```json
[
  {
    "app": "Firefox",
    "title": "GitHub - example/repo - Mozilla Firefox",
    "id": 12345678,
    "bounds": {"X": 0, "Y": 25, "Width": 1920, "Height": 1055},
    "area": 2025600
  },
  {
    "app": "gnome-terminal",
    "title": "Terminal",
    "id": 23456789,
    "bounds": {"X": 100, "Y": 100, "Width": 800, "Height": 600},
    "area": 480000
  }
]
```

**Get Active Window Info:**
Returns JSON with info about the currently focused window.

```bash
python3 capture-screen-linux/window_info.py active
```

### 2. Take Screenshots

#### Full Screen Screenshot
Run the python script with the `full` command:
```bash
python3 capture-screen-linux/screenshot.py full <output_path>
```

Example:
```bash
python3 capture-screen-linux/screenshot.py full /tmp/my_screen.png
```

#### Window Screenshot
Run the python script with the `window` command, specifying the application name or window title and output path.

⚠️ **Important**: Use the app name or title from `window_info.py list` output. The script supports fuzzy matching (searches in both app name and title).

```bash
python3 capture-screen-linux/screenshot.py window "<App Name or Title>" <output_path>
```

Examples:
```bash
python3 capture-screen-linux/screenshot.py window "Firefox" /tmp/firefox_window.png
python3 capture-screen-linux/screenshot.py window "gnome-terminal" /tmp/terminal_window.png
python3 capture-screen-linux/screenshot.py window "Code" /tmp/vscode_window.png
```

#### List Windows (Human Readable)
To see a formatted table of windows for quick reference:
```bash
python3 capture-screen-linux/screenshot.py list
```

## Dependencies & Requirements
- Linux with X11 display server (most desktop environments)
- Python 3
- Required Python packages:
  - `python-xlib` - For X11 window management
- Required system tools (at least one):
  - `scrot` (recommended) - Simple screenshot tool
  - `imagemagick` (import command) - Advanced image manipulation
  - `gnome-screenshot` - GNOME desktop screenshot tool

### Installation

#### Ubuntu/Debian
```bash
# Python dependencies
pip3 install python-xlib

# System screenshot tools (install at least one)
sudo apt-get install scrot imagemagick gnome-screenshot
```

#### Fedora/RHEL
```bash
# Python dependencies
pip3 install python-xlib

# System screenshot tools (install at least one)
sudo dnf install scrot ImageMagick gnome-screenshot
```

#### Arch Linux
```bash
# Python dependencies
pip3 install python-xlib

# System screenshot tools (install at least one)
sudo pacman -S scrot imagemagick gnome-screenshot
```

## Technical Details

### Window Identification
- Uses X11 protocol via `python-xlib`
- Queries `_NET_WM_NAME` for window titles
- Queries `WM_CLASS` property for application names
- Filters visible windows using `IsViewable` state
- Sorts by area to prioritize main windows

### Screenshot Methods
- **Full screen**: Uses `scrot`, `gnome-screenshot`, or `import` (ImageMagick)
- **Window capture**: Uses `import` with window ID (most reliable method)
- Automatically detects available tools and uses the best option
- Outputs PNG format images

### Display Server Support
- ✅ **X11**: Full support with window enumeration
- ⚠️ **Wayland**: Limited support
  - Full screen screenshots work with most tools
  - Window-specific capture may not work due to Wayland security restrictions
  - Consider using X11 compatibility mode (XWayland) for full functionality

## Troubleshooting

### "Cannot connect to X display" Error
This means you're not running under X11 or the DISPLAY variable is not set.

**Solutions:**
- Ensure you're running in a graphical environment
- Check if X11 is running: `echo $DISPLAY` (should show something like `:0`)
- If using SSH, enable X11 forwarding: `ssh -X user@host`

### Import Errors
If you see `ImportError: No module named 'Xlib'`:
```bash
pip3 install python-xlib
```

### No Screenshot Tool Available
Install at least one screenshot tool:
```bash
# Recommended - lightweight and fast
sudo apt-get install scrot

# Alternative - more features
sudo apt-get install imagemagick
```

### Wayland Compatibility
If you're running Wayland and experiencing issues:

1. Check your session type: `echo $XDG_SESSION_TYPE`
2. For window-specific captures, switch to X11 session at login
3. Or use XWayland compatibility:
   ```bash
   # Force app to run under XWayland
   GDK_BACKEND=x11 python3 capture-screen-linux/screenshot.py ...
   ```

### Window Not Found
If a window isn't being captured:
1. Ensure the window is visible (not minimized)
2. Check the exact app name or title using the `list` command
3. Try using a partial match from the title
4. For flatpak/snap apps, the app name might be different (check with `list`)

### Permission Issues
Some desktop environments may require additional permissions for screenshot tools:
- GNOME may show a permission dialog on first use
- Grant "Screenshot" permission in privacy settings if prompted

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/langqi99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
