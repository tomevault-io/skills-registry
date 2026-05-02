---
name: makepad-screenshot
description: | Use when this capability is needed.
metadata:
  author: zhanghandong
---

# Makepad Screenshot Skill

> Automated screenshot debugging for Makepad GUI applications

## Trigger Words

- `/screenshot` - Capture current running Makepad app
- `/screenshot <app-name>` - Capture specific app
- `/run-and-capture <package>` - Build, run and capture
- "截图", "看看UI", "查看界面", "capture makepad"

## Features

Automates visual debugging workflow for Makepad apps:
1. Bring app window to front
2. Capture screenshot
3. Analyze screenshot with Read tool
4. Report UI issues or confirm correct rendering

## Usage

### 1. Capture Current Running App

```
/screenshot
```

Auto-detects and captures windows containing "makepad" or current project name.

### 2. Capture Specific App

```
/screenshot a2ui-demo
```

Captures app with window name containing "a2ui".

### 3. Build, Run and Capture

```
/run-and-capture a2ui-demo
```

Full workflow: cargo build → cargo run → wait for startup → capture.

## Implementation Steps

### Step 1: Detect Running App

```bash
# Find Makepad-related processes
ps aux | grep -E "(makepad|cargo.*run)" | grep -v grep
```

### Step 2: Bring Window to Front (macOS)

```bash
# Generic pattern - match by process name
osascript -e 'tell application "System Events" to set frontmost of (first process whose name contains "APP_NAME") to true'

# Example
osascript -e 'tell application "System Events" to set frontmost of (first process whose name contains "a2ui") to true'
```

### Step 3: Capture to Scratchpad

```bash
# Silent capture (-x suppresses shutter sound)
screencapture -x $SCRATCHPAD/screenshot.png
```

### Step 4: View with Read Tool

```
Read $SCRATCHPAD/screenshot.png
```

Claude's Read tool supports image files and can directly analyze screenshot content.

## Complete Automation Script

### Capture Current App

```bash
APP_NAME="${1:-makepad}"
SCRATCHPAD="${SCRATCHPAD_DIR:-/tmp}"
SCREENSHOT="$SCRATCHPAD/makepad_screenshot_$(date +%H%M%S).png"

# Bring to front
osascript -e "tell application \"System Events\" to set frontmost of (first process whose name contains \"$APP_NAME\") to true" 2>/dev/null

sleep 0.5

# Capture
screencapture -x "$SCREENSHOT"

echo "$SCREENSHOT"
```

### Build, Run and Capture

```bash
PACKAGE="$1"
SCRATCHPAD="${SCRATCHPAD_DIR:-/tmp}"
SCREENSHOT="$SCRATCHPAD/${PACKAGE}_$(date +%H%M%S).png"

# Kill old process
pkill -f "$PACKAGE" 2>/dev/null

# Build and run
cargo build -p "$PACKAGE" && \
(cargo run -p "$PACKAGE" 2>&1 &) && \
sleep 5 && \
osascript -e "tell application \"System Events\" to set frontmost of (first process whose name contains \"$PACKAGE\") to true" && \
sleep 1 && \
screencapture -x "$SCREENSHOT"

echo "$SCREENSHOT"
```

## Claude Execution Flow

When user says "截图" or "/screenshot":

1. **Detect App**
   ```bash
   ps aux | grep -E "cargo.*run|makepad" | grep -v grep | head -1
   ```

2. **Determine App Name**
   - Extract from process list
   - Or use current project's package name

3. **Execute Screenshot**
   ```bash
   osascript -e 'tell application "System Events" to set frontmost of (first process whose name contains "APP") to true'
   sleep 0.5
   screencapture -x /path/to/scratchpad/screenshot.png
   ```

4. **Read and Analyze**
   ```
   Read /path/to/scratchpad/screenshot.png
   ```

5. **Report Results**
   - Describe visible UI elements
   - Point out any issues (layout, colors, missing components)
   - Confirm functionality

## Notes

- **macOS Only** - Uses `screencapture` and `osascript`
- **Permission Required** - osascript needs accessibility permission
- **Window Occlusion** - Must bring to front first
- **Startup Time** - Need to sleep after cargo run to wait for window
- **Scratchpad** - Use session-specific scratchpad directory for screenshots

## Example Interactions

### Example 1: Quick Screenshot

```
User: 截图看看现在的 UI

Claude:
1. Detected running a2ui-demo
2. Execute screenshot command
3. Read screenshot file
4. Analysis: "UI displays normally, TextField shows input '你好', product list rendered 3 cards..."
```

### Example 2: Build Run and Capture

```
User: /run-and-capture a2ui-demo

Claude:
1. pkill old process
2. cargo build -p a2ui-demo
3. cargo run -p a2ui-demo &
4. sleep 5 wait for startup
5. Capture and analyze
```

### Example 3: Verify Two-way Binding

```
User: I entered "Hello😊" in TextField, help me verify two-way binding with screenshot

Claude:
1. Execute screenshot, save to scratchpad
2. Read screenshot, analyze UI
3. Output verification report:

   ┌────────────────────────────────────────┐
   │ A2UI Demo - Product Catalog           │
   ├────────────────────────────────────────┤
   │ [Search Products]                      │
   │ ┌──────────────────────────────────┐  │
   │ │ Hello😊                          │  │ ← TextField shows input
   │ └──────────────────────────────────┘  │
   │ Hello😊                               │ ← Text (caption) synced
   │                                        │
   │ Products (Template Rendering)          │
   │ ┌──────────────────────────────────┐  │
   │ │ Premium Headphones | $99.99 | +  │  │
   │ └──────────────────────────────────┘  │
   └────────────────────────────────────────┘

   ✅ Two-way binding verification successful:
   | Component | Binding Path | Display Content |
   |-----------|--------------|-----------------|
   | TextField | /search/query | Hello😊 |
   | Text (caption) | /search/query | Hello😊 |
```

## UI Analysis Report Format

After screenshot, Claude should provide structured analysis:

### Basic Report

```markdown
## Screenshot Analysis

**Application**: a2ui-demo
**Time**: 2026-02-01 14:30:00
**Window State**: Normal display

### UI Elements

| Element | Type | Content/State |
|---------|------|---------------|
| Title | Label | "A2UI Demo - Product Catalog" |
| Search Box | TextField | Empty/Has input |
| Product List | PortalList | 3 cards |

### Issue Detection

- ✅ Layout normal
- ✅ Text rendering normal
- ⚠️ Image not loaded (network image)
```

### Function Verification Report

```markdown
## Function Verification: TextField Two-way Binding

**Test Scenario**: User enters content in TextField, verify data model sync

### Verification Result

| Component | Binding Path | Expected | Actual | Status |
|-----------|--------------|----------|--------|--------|
| TextField | /search/query | Hello😊 | Hello😊 | ✅ |
| Text (caption) | /search/query | Hello😊 | Hello😊 | ✅ |

### Conclusion

✅ **Two-way binding works correctly**
- Unicode Chinese characters: ✅
- Emoji characters: ✅
- Real-time sync: ✅
```

## Common Issue Troubleshooting

### Window Cannot Be Brought to Front

```bash
# Check if process is running
ps aux | grep -E "a2ui|makepad" | grep -v grep

# Try using process ID
osascript -e 'tell application "System Events" to set frontmost of (first process whose unix id is PID) to true'
```

### Screenshot is Black Screen

Possible causes:
1. Window occluded by other windows - bring to front first
2. App still starting - increase sleep time
3. GPU rendering delay - wait 1-2 seconds before capture

### App Name Matching Issues

Makepad compiled app names are usually package names (underscores to hyphens):
- `a2ui-demo` → process name contains `a2ui` or `a2ui-demo`
- `my_app` → process name contains `my_app` or `my-app`

## Related Skills

- `makepad-basics` - Makepad fundamentals
- `makepad-widgets` - Widget usage
- `memory-skills` - Memory system

---

**Context**: For automated visual debugging of Makepad GUI applications
**Tags**: makepad, screenshot, debugging, gui, automation, macos

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zhanghandong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
