---
name: axe
description: iOS Simulator automation using AXe CLI - tap, swipe, type, screenshot, and accessibility inspection Use when this capability is needed.
metadata:
  author: joehoel
---

# AXe - iOS Simulator Automation

AXe is a CLI tool for interacting with iOS Simulators using Apple's Accessibility APIs and HID functionality.

## Prerequisites

- macOS with Xcode and iOS Simulators installed
- AXe installed via `brew install cameroncooke/axe/axe`
- A booted iOS Simulator

## Getting Started

### List Available Simulators

```bash
axe list-simulators
```

This returns a pipe-delimited list of simulators with format: `UDID | Name | State | Device | OS`

### Get Simulator UDID

```bash
# Get the UDID of the first booted simulator
UDID=$(axe list-simulators | grep "Booted" | head -1 | cut -d'|' -f1 | tr -d ' ')
```

## Core Commands

### Tap

Tap at coordinates or by accessibility identifier:

```bash
# Tap at specific coordinates
axe tap -x 100 -y 200 --udid $UDID

# Tap by accessibility identifier (testID in React Native)
axe tap --id "submit-button" --udid $UDID

# Tap by accessibility label
axe tap --label "Submit" --udid $UDID

# With timing controls
axe tap -x 100 -y 200 --pre-delay 1.0 --post-delay 0.5 --udid $UDID
```

### Type Text

```bash
# Type text directly (use single quotes for special characters)
axe type 'Hello World!' --udid $UDID

# Type from stdin (best for automation)
echo "Complex text with special chars: @#$%" | axe type --stdin --udid $UDID

# Type from file
axe type --file input.txt --udid $UDID
```

### Swipe Gestures

```bash
# Basic swipe
axe swipe --start-x 100 --start-y 300 --end-x 300 --end-y 100 --udid $UDID

# Swipe with duration and delta control
axe swipe --start-x 50 --start-y 500 --end-x 350 --end-y 500 --duration 2.0 --delta 25 --udid $UDID
```

### Gesture Presets

Common gestures are available as presets:

```bash
# Scrolling
axe gesture scroll-up --udid $UDID
axe gesture scroll-down --udid $UDID
axe gesture scroll-left --udid $UDID
axe gesture scroll-right --udid $UDID

# Edge swipes (navigation)
axe gesture swipe-from-left-edge --udid $UDID    # Back navigation
axe gesture swipe-from-right-edge --udid $UDID   # Forward navigation
axe gesture swipe-from-top-edge --udid $UDID     # Dismiss/close
axe gesture swipe-from-bottom-edge --udid $UDID  # Open/reveal

# With custom screen dimensions
axe gesture scroll-up --screen-width 430 --screen-height 932 --udid $UDID

# With timing
axe gesture scroll-down --pre-delay 1.0 --post-delay 0.5 --udid $UDID
```

### Hardware Buttons

```bash
axe button home --udid $UDID
axe button lock --udid $UDID
axe button lock --duration 2.0 --udid $UDID  # Long press
axe button siri --udid $UDID
axe button apple-pay --udid $UDID
axe button side-button --udid $UDID
```

### Low-Level Touch Control

For advanced gesture sequences:

```bash
# Touch down
axe touch -x 150 -y 250 --down --udid $UDID

# Touch up
axe touch -x 150 -y 250 --up --udid $UDID

# Touch down then up with delay
axe touch -x 150 -y 250 --down --up --delay 1.0 --udid $UDID
```

### Keyboard Control

```bash
# Press single key by HID keycode
axe key 40 --udid $UDID                    # Enter key
axe key 42 --duration 1.0 --udid $UDID     # Hold Backspace

# Key sequences (keycodes for "hello")
axe key-sequence --keycodes 11,8,15,15,18 --udid $UDID
```

## Accessibility Inspection

### Describe UI

Get accessibility tree information:

```bash
# Full screen accessibility tree
axe describe-ui --udid $UDID

# Note: --point option may not be available in all versions
```

The output includes accessibility identifiers, labels, roles, and element positions - useful for finding elements to tap.

## Screenshots and Recording

### Screenshot

```bash
# Auto-generated filename in current directory
axe screenshot --udid $UDID

# Specific output file (use project's .axe/ folder for organization)
mkdir -p .axe && axe screenshot --output .axe/screenshot.png --udid $UDID

# Save to directory with auto-generated name
axe screenshot --output .axe/ --udid $UDID
```

**Best Practice**: Save screenshots to `.axe/` folder in the project root for easy access and to keep them organized. Add `.axe/` to `.gitignore`.

### Video Recording

```bash
# Record to MP4 (press Ctrl+C to stop)
axe record-video --udid $UDID --fps 15 --output recording.mp4

# Auto-generated filename
axe record-video --udid $UDID --fps 20

# Lower quality for smaller files
axe record-video --udid $UDID --fps 10 --quality 60 --scale 0.5 --output low-bandwidth.mp4
```

### Video Streaming

```bash
# Stream MJPEG to stdout
axe stream-video --udid $UDID --fps 10 --format mjpeg > stream.mjpeg

# Pipe to ffmpeg
axe stream-video --udid $UDID --fps 30 --format ffmpeg | \
  ffmpeg -f image2pipe -framerate 30 -i - -c:v libx264 -preset ultrafast output.mp4
```

## Common Automation Patterns

### Wait and Tap

```bash
# Wait 2 seconds, tap, wait 1 second after
axe tap --id "button" --pre-delay 2.0 --post-delay 1.0 --udid $UDID
```

### Scroll Until Element Visible

```bash
# Scroll down repeatedly until element found
while ! axe describe-ui --udid $UDID | grep -q "target-element"; do
  axe gesture scroll-down --udid $UDID
  sleep 0.5
done
axe tap --id "target-element" --udid $UDID
```

### Type in Text Field

```bash
# Tap field, wait, then type
axe tap --id "email-input" --udid $UDID
sleep 0.3
axe type 'user@example.com' --udid $UDID
```

### Navigate Back

```bash
axe gesture swipe-from-left-edge --udid $UDID
```

## Integration with React Native/Expo

In React Native, use `testID` prop to set accessibility identifiers:

```tsx
<Button testID="submit-button" title="Submit" />
```

Then tap it with:

```bash
axe tap --id "submit-button" --udid $UDID
```

## Troubleshooting

### No Simulators Found

Ensure a simulator is booted:
```bash
xcrun simctl list devices | grep Booted
```

### Element Not Found

Use `describe-ui` to inspect available elements:
```bash
axe describe-ui --udid $UDID | grep -i "button"
```

### Timing Issues

Add delays between actions:
```bash
axe tap --id "button" --pre-delay 0.5 --post-delay 0.5 --udid $UDID
```

## Key HID Keycodes Reference

| Key | Keycode |
|-----|---------|
| Enter | 40 |
| Backspace | 42 |
| Tab | 43 |
| Space | 44 |
| Escape | 41 |
| Delete | 76 |

## Additional Resources

- [AXe GitHub Repository](https://github.com/cameroncooke/AXe)
- [Usage Examples](https://github.com/cameroncooke/AXe/blob/main/USAGE_EXAMPLES.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joehoel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
