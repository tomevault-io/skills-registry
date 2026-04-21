---
name: clawdbot-ui-development
description: ClawdBot control panel UI requirements and architecture Use when this capability is needed.
metadata:
  author: ssujitx
---

# ClawdBot UI Development

Complete guide for building the ClawdBot Control Panel - a PyQt6 desktop app for managing ClawdBot gateway, plugins, and settings.

## Tech Stack

- **UI Framework**: PyQt6
- **Package Manager**: UV (uv add, uv run)
- **Python**: 3.11+
- **Platforms**: Windows + macOS

## Architecture

```
clawdbot-ui/
├── pyproject.toml
├── main.py                   # Entry point
├── src/
│   ├── app.py                # Application setup
│   ├── ui/
│   │   ├── window.py         # Main window (all pages in one file)
│   │   ├── terminal.py       # Log viewer widget
│   │   ├── command_dialog.py # Modal command runner
│   │   └── styles.py         # Dark theme CSS
│   ├── core/
│   │   ├── process.py        # Background process runner
│   │   └── version.py        # Version checking (GitHub API)
│   └── utils/
│       └── platform.py       # Platform detection
└── .agent/skills/            # 9 skills
```

## Navigation Structure

**4 Pages** (tabs in sidebar):
1. **Dashboard** - Gateway control, version info, logs
2. **ClawdHub** - Plugin manager + Skills Management
3. **Terminal** - Run any command with admin privileges
4. **Settings** - Version display, maintenance operations

**Sidebar Features**:
- Brand section (logo, title, version)
- Quick Links (Skills, Docs) - open external URLs
- Status panel - gateway status + quick stop button

---

# Dashboard Page

Main landing page for ClawdBot - manages gateway lifecycle and displays real-time logs.

## Features

### Gateway Control Card

**Service Status Indicator**
- Green "● Running" when gateway active
- Gray "● Stopped" when inactive  
- Yellow "● Not Installed" when ClawdBot missing

**Control Buttons** (all 110px width)
- **Start Service**: Starts gateway (visible when stopped)
- **Stop Service**: Stops gateway (visible when running)
- **Open Gateway**: Opens `http://127.0.0.1:18789` (visible when running)
- **Open UI**: Gets dashboard URL with token via `clawdbot dashboard`, opens in browser (visible when running)

### Installation Management

**Install ClawdBot** (green button)
- Platform-specific install command
- Progress shown in terminal

**Update Available**
- Shows when newer version detected
- Format: "Update to vX.X.X"

**Uninstall** (red outline button)
- Runs `clawdbot uninstall --all --yes`
- Opens in CommandDialog

### Service Logs Terminal

- Real-time streaming from gateway process
- ANSI color support
- Auto-scroll (pauseable)
- **Copy Logs** button (100px × 28px)
- **Log dividers** (`━━━━━━━`) after each command for clarity

**Key Methods**:
```python
_on_start()           # Start gateway
_on_stop()            # Stop gateway
_on_open_gateway()    # Open http://127.0.0.1:18789
_on_open_ui()         # Get token, open dashboard
_on_install()         # Install ClawdBot
_on_update()          # Update ClawdBot
_copy_logs()          # Copy terminal to clipboard
```

---

# ClawdHub Page

Plugin and skills manager for ClawdBot.

## Features

### ClawdHub CLI Management

**Installation Status** (auto-detected)
- "Checking..." on load
- "Installed (v1.2.3)" or "Not installed"

**Action Buttons** (fixed widths)
- `[Install]` (green) → `npm install -g clawdhub undici`
- `[Update]` (white) → `npm update -g clawdhub undici`
- `[Uninstall]` (red) → `npm uninstall -g clawdhub`

### Skills Management Card

**Skill Count Display**: "X skills installed"
- Auto-updates after successful installation
- Counts folders in `{user_home}/clawd/skills/`

**Open Skills Folder Button** (140px)
- Opens skills folder in File Explorer/Finder
- Creates folder if doesn't exist
- Path: `C:\Users\{username}\clawd\skills\` (Windows)

### Install Skills Card

**Input Field**
- Placeholder: `spotify or self-improving-agent`
- Auto-prepends `clawdhub install`
- Press Enter or click **Install** (80px × 36px)

**Example Skills**:
```bash
spotify
youtube
self-improving-agent
```

### ClawdHub Logs

- Separate terminal from Dashboard
- Real-time output
- **Copy Logs** button
- Min height: 150px

**Key Methods**:
```python
_on_install_clawdhub()     # Install ClawdHub
_on_update_clawdhub()      # Update ClawdHub
_on_uninstall_clawdhub()   # Remove ClawdHub
_check_clawdhub()          # Detect install status
_on_run_clawdhub_cmd()     # Install skill (auto-prepends clawdhub install)
_on_open_skills_folder()   # Open skills folder
```

---

# Terminal Page

Run any command with admin privileges - general-purpose command execution.

## Features

### Run Command Card

**Input Field**
- Placeholder: `clawdbot configure or npm --version`
- No auto-prefix - runs exactly what you type
- Press Enter or click **Execute** (90px × 36px)

**Admin Execution** (Windows)
- Uses PowerShell with `Start-Process -Verb RunAs`
- macOS: Uses `osascript` for admin privileges
- Linux: Uses `sudo -S`

### Terminal Output

**Buttons**:
- **Copy Logs** (100px × 28px) - copy all output
- **Clear Logs** (100px × 28px) - clear terminal

**Output Features**:
- Real-time streaming
- Auto-scroll to bottom
- Command echo: `$ clawdbot configure`
- Completion: `✓ Command completed successfully` or `✗ Command failed`
- Separator: `━━━━━━` after each command

**Key Methods**:
```python
_on_execute_terminal_cmd()    # Execute command with admin
_on_terminal_cmd_finished()   # Handle completion
_copy_terminal_logs()         # Copy output to clipboard
_clear_terminal_logs()        # Clear terminal
```

---

# Settings Page

Configuration and maintenance operations.

## Features

### About ClawdBot Card

**Version Information** (auto-updated)
```
Version: v2026.1.24-3
Latest: v2026.1.24-3
Gateway: Running on port 18789
```

### Maintenance Card

**Action Buttons**
- **Check for Updates** (auto-width) → `clawdbot update`
- **Reset** (80px) → `clawdbot reset --yes`
- **Uninstall** (100px, red) → `clawdbot uninstall --all --yes`

All open in CommandDialog for progress display.

**Key Methods**:
```python
_on_update()      # Update ClawdBot
_on_reset()       # Reset workspace/config
_on_uninstall()   # Remove ClawdBot
```

---

# Styling System

**File**: `src/ui/styles.py`

## Button Styles

All buttons use fixed widths for consistency:

```python
# actionBtn - White with black text
padding: 6px 14px
font-size: 12px
font-weight: 500

# dangerBtn - Red outline (matches actionBtn size)
background: transparent
color: #f85149
border: 1px solid #f85149
padding: 6px 14px
font-size: 12px

# primaryBtn - Cyan/teal
background: #00bfa5
color: #000000

# successBtn - Green
background: #22c55e
color: #000000
```

## Dark Theme Colors

```css
Background: #0a0f1a
Sidebar: #0f1419
Cards: #1a2535
Borders: #2a3545
Text: #ffffff / #b0b8c4 / #6b7280
Success: #22c55e
Error: #ef4444
Warning: #f59e0b
```

---

# Platform-Specific Commands

### Windows
```python
["powershell", "-Command", "clawdbot gateway"]
```

### macOS/Linux
```python
["clawdbot", "gateway"]
```

Platform detection via `is_windows()` utility.

---

# Key Patterns

## Thread Safety
```python
# ✅ Thread-safe
signal.emit(data)

# ❌ NOT thread-safe from worker thread
widget.setText(x)
```

## Process Management
```python
# Store runner as class variable
self.gateway_runner = ProcessRunner(cmd)
self.gateway_runner.output.connect(self.terminal.log)
self.gateway_runner.start()
```

## Graceful Shutdown
```python
def closeEvent(self, event):
    if self.gateway_runner:
        self.gateway_runner.stop()
        self.gateway_runner.wait(3000)
    event.accept()
```

## Log Dividers

After each command completion:
```python
self.terminal.log("━" * 60)
```

Makes logs more readable when multiple commands run.

---

# File Organization

**Why window.py is NOT split**:
- Heavy state sharing across pages (`self.terminal`, `self.version_status`, etc.)
- Dashboard actions update Settings display
- Clear organization with `_create_*_page()` methods
- Splitting would require complex SharedState class

**What IS separate**:
- `terminal.py` - Reusable widget
- `command_dialog.py` - Reusable modal
- `styles.py` - Centralized styling

---

# Development Tips

1. **Button sizing**: Always use `setFixedWidth()` for uniform appearance
2. **Visibility**: Toggle with `setVisible()` based on state
3. **Threading**: Store ProcessRunner as class variable to prevent crashes
4. **Updates**: Call `_check_version()` after all install/update/uninstall ops
5. **Logs**: Add dividers after command completion for readability

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ssujitx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
