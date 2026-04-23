---
name: lg-demo-recorder
description: Captures full demo evidence — screenshots, screen recordings, and GIFs — from emulators or physical devices for documentation, README media, and GSoC submission. Use when this capability is needed.
metadata:
  author: ashishyesale7
---

# LG Demo Recorder

## Overview

This skill captures visual evidence of your running Liquid Galaxy Flutter app. It produces screenshots, screen recordings (MP4), and animated GIFs suitable for README files, contest submissions, Google Drive deliverables, and mentor demos.

**This is NOT optional.** The Gemini Summer of Code contest requires visual proof that the app runs, connects to the LG rig (or demonstrates UI), and performs the required LG operations (logo, KML, flyTo, clean).

**Announce at start:** "I'm using the lg-demo-recorder skill to capture demo evidence for [purpose]."

**GUARDRAIL**: The **Critical Advisor** (.agent/skills/lg-critical-advisor/SKILL.md) is active. Demo evidence is required for submission — students cannot skip this.

## Prerequisites

- App must be running on an emulator or physical device (use `lg-emulator-manager` to launch)
- `adb` available on PATH (for Android)
- `xcrun simctl` available (for iOS on macOS)
- `docs/screenshots/`, `docs/recordings/`, `docs/gifs/` directories exist

## Phase 1: Screenshot Capture

### Android (via ADB)
```bash
# Single screenshot
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
adb exec-out screencap -p > docs/screenshots/screen_${TIMESTAMP}.png
echo "Screenshot saved: docs/screenshots/screen_${TIMESTAMP}.png"
```

### iOS Simulator (macOS)
```bash
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
xcrun simctl io booted screenshot docs/screenshots/screen_${TIMESTAMP}.png
echo "Screenshot saved: docs/screenshots/screen_${TIMESTAMP}.png"
```

### Capture Set — All Key Screens
Run the app through each screen and capture:

| Screen | Filename | Purpose |
|--------|----------|---------|
| Splash | `splash.png` | App startup branding |
| Connection | `connection.png` | LG rig connection UI |
| Main/Home | `main_screen.png` | Primary controller interface |
| Visualization | `viz_active.png` | Active KML on Google Earth |
| Settings | `settings.png` | Configuration screen |
| About/Help | `help.png` | Information screen |

```bash
# Capture all key screens in sequence
for SCREEN in splash connection main_screen viz_active settings help; do
  echo "Navigate to $SCREEN, then press Enter..."
  read
  adb exec-out screencap -p > "docs/screenshots/${SCREEN}.png"
  echo "  ✓ Captured ${SCREEN}.png"
done
echo "All screenshots captured!"
```

## Phase 2: Screen Recording

### Android (via ADB — max 180 seconds default)
```bash
# Start recording (runs in background, Ctrl+C or timeout to stop)
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
echo "Recording started... Demonstrate the app. Press Ctrl+C to stop."
adb shell screenrecord --time-limit 60 /sdcard/demo_${TIMESTAMP}.mp4

# Pull the recording to local machine
adb pull /sdcard/demo_${TIMESTAMP}.mp4 docs/recordings/demo_${TIMESTAMP}.mp4
adb shell rm /sdcard/demo_${TIMESTAMP}.mp4
echo "Recording saved: docs/recordings/demo_${TIMESTAMP}.mp4"
```

### iOS Simulator (macOS)
```bash
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
echo "Recording started... Demonstrate the app. Press Ctrl+C to stop."
xcrun simctl io booted recordVideo docs/recordings/demo_${TIMESTAMP}.mp4
echo "Recording saved: docs/recordings/demo_${TIMESTAMP}.mp4"
```

### Demo Script (What to Record)
Walk through this sequence during recording:

1. **App launch** — show splash screen (2-3 seconds)
2. **Connection** — connect to LG rig (or show the connection UI)
3. **Main screen** — show all available actions
4. **Send KML** — demonstrate sending a visualization to the rig
5. **FlyTo** — navigate Google Earth to a location
6. **Orbit** — start an orbit around a POI
7. **Clean** — clear all KML from the rig
8. **Settings** — show configuration options
9. **Disconnect** — clean disconnect

## Phase 3: GIF Generation

### From Recording (using ffmpeg)
```bash
# Check if ffmpeg is available
which ffmpeg || echo "Install ffmpeg: brew install ffmpeg (macOS) or sudo apt install ffmpeg (Linux)"

# Convert MP4 to GIF (good quality, reasonable size)
RECORDING="docs/recordings/demo_latest.mp4"
ffmpeg -i "$RECORDING" \
  -vf "fps=10,scale=320:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" \
  -loop 0 \
  docs/gifs/demo.gif

echo "GIF saved: docs/gifs/demo.gif"
```

### Quick GIF (lower quality, smaller file)
```bash
ffmpeg -i "$RECORDING" \
  -vf "fps=8,scale=240:-1" \
  -loop 0 \
  docs/gifs/demo_small.gif
```

## Phase 4: README Integration

Add demo media to the project README:

```markdown
## Demo

### Screenshots
| Splash | Connection | Main Screen |
|--------|-----------|-------------|
| ![Splash](docs/screenshots/splash.png) | ![Connection](docs/screenshots/connection.png) | ![Main](docs/screenshots/main_screen.png) |

### Video Demo
![App Demo](docs/gifs/demo.gif)

> Full video: [docs/recordings/demo.mp4](docs/recordings/demo.mp4)
```

## Phase 5: Contest Deliverable Packaging

For the contest submission folder:

```bash
# Create deliverable package
DELIVERABLE_DIR="deliverables/$(date +%Y%m%d)_v1.0"
mkdir -p "$DELIVERABLE_DIR"

# Copy all evidence
cp docs/screenshots/*.png "$DELIVERABLE_DIR/"
cp docs/recordings/*.mp4 "$DELIVERABLE_DIR/"
cp docs/gifs/*.gif "$DELIVERABLE_DIR/"

# Copy APK (required for Task 2)
cp flutter_client/build/app/outputs/flutter-apk/app-release.apk "$DELIVERABLE_DIR/"

echo "Deliverable package ready at: $DELIVERABLE_DIR"
ls -la "$DELIVERABLE_DIR"
```

### Contest Evidence Checklist (Task 2)

The student MUST capture evidence of each Task 2 requirement:

| Task 2 Requirement | Screenshot Name | What to Show |
|--------------------|-----------------|----|
| **Send LG Logo** | `task2_logo_sent.png` | Logo visible on left slave screen |
| **Send 3D Pyramid KML** | `task2_pyramid.png` | Colored 3D pyramid on Google Earth |
| **FlyTo Home City** | `task2_flyto.png` | Google Earth showing student's home city |
| **Clean Logos** | `task2_clean_logo.png` | Left screen cleared of logo |
| **Clean KMLs** | `task2_clean_kml.png` | Google Earth cleared of all KML |
| **App UI** | `task2_ui_dashboard.png` | The phone controller UI with action buttons |
| **Connection** | `task2_connected.png` | Connection screen showing "Connected" state |
| **Release APK** | `app-release.apk` | Built via `flutter build apk --release` |

> *"If you don't have access to a physical LG rig, capture the UI screens and the SSH connection attempt. Document clearly in the README what each button does."*

## Checklist

- [ ] Splash screen screenshot captured
- [ ] All key screen screenshots captured
- [ ] Full demo video recorded (30-60 seconds)
- [ ] GIF generated for README
- [ ] README updated with media
- [ ] Evidence committed to git
- [ ] Deliverable package prepared for Google Drive

## Handoff

- **Evidence captured** → Return to calling workflow (usually `full-pipeline` Stage 10 Quiz & Graduation)
- **App not running** → `.agent/skills/lg-emulator-manager/SKILL.md` to launch emulator first
- **ffmpeg missing** → `.agent/skills/lg-setup-guide/SKILL.md` for install guidance
- **Need more testing** → `.agent/skills/lg-tester/SKILL.md` for automated tests

## 🔗 Skill Chain

After demo evidence is captured and committed, **automatically offer the next stage**:

> *"Demo evidence is captured and committed! Screenshots, recordings, and GIFs are ready for your submission. Time for the final quiz to validate your understanding of the entire project. Ready for graduation? 🎓"*

If student says "ready" → activate `.agent/skills/lg-quiz-master/SKILL.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashishyesale7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
