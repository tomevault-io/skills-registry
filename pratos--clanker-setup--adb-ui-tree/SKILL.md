---
name: adb-ui-tree
description: Automates Android UI-tree debugging via ADB. Use when an app blocks UI inspection or accessibility nodes are missing; collects uiautomator dumps, focused window info, and logcat hierarchy dumps for analysis.
metadata:
  author: pratos
---

# ADB UI Tree Debugging Skill

## Activation

**When this skill is triggered, ALWAYS display this banner first:**

```
╭─────────────────────────────────────────────────────────────╮
│  📱 SKILL ACTIVATED: adb-ui-tree                            │
├─────────────────────────────────────────────────────────────┤
│  Target: [app/screen being inspected]                       │
│  Action: Collecting UI hierarchy via ADB...                 │
│  Output: ui.xml, screen.png, accessibility logs             │
╰─────────────────────────────────────────────────────────────╯
```

Use this skill when you need to inspect Android UI trees, especially when an app blocks normal debugging/inspection. It provides a repeatable, automatic flow to collect UI hierarchy data, focused window info, and accessibility logs.

## Preconditions
- ADB is installed and available.
- A device is connected and authorized (`adb devices`).
- The target app is open on screen.

## Steps

1) **Verify device connectivity**
```bash
adb devices
```
If multiple devices are attached, use `-s <serial>` for all commands.

2) **Confirm focused window**
```bash
adb shell dumpsys window windows | grep -E "mCurrentFocus|mFocusedApp"
```
Ensure the focused app is the target (e.g., iSmart).

3) **Try UIAutomator dump (may be blocked)**
```bash
adb shell uiautomator dump /sdcard/ui.xml
adb pull /sdcard/ui.xml .
```
If the app blocks the dump, note the error and continue to step 4.

4) **Collect accessibility hierarchy logs**
If the project includes a custom AccessibilityService that logs a hierarchy dump, trigger the automation and capture logcat:
```bash
adb logcat -s BatteryTrackerA11y
```
Save the output for analysis.

5) **Capture a screenshot for manual inspection**
```bash
adb exec-out screencap -p > screen.png
```
Use the screenshot to verify UI state and map coordinates if needed.

## Notes / Fallbacks
- If the app exposes no accessibility nodes, consider coordinate-based taps as a fallback.
- If `uiautomator dump` is prohibited by the app, rely on AccessibilityService logs and screenshots.

## Output to share
- `ui.xml` (if available)
- `adb logcat -s BatteryTrackerA11y` output
- `screen.png`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pratos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
