---
name: test-loop
description: Build, launch, and test an iOS Swift app on the simulator in an automated loop. Inspects the UI, finds issues, fixes the Swift code, rebuilds, and retests until the app works correctly. Use when this capability is needed.
metadata:
  author: webcoyote
---

# iOS Test Pilot: Build-Test-Fix Loop

Build an iOS app, launch it on the simulator, inspect and test the UI, fix issues, and repeat until it works.

## Arguments

First argument: **build command** (required). Remaining text: **test description** (optional).

If no test description, do a general inspection: build, launch, screenshot, read the accessibility tree, report what you see.

## Protocol

### 1. Prepare

If the build command is a custom script, read it first to understand what it handles (compile only? install/launch too?). This determines which later steps you can skip.

### 2. Build

Run the build command. On failure, don't dump the full log — scan for lines containing `error:` and the few lines around them. Read the referenced source file, fix the issue, and rebuild. Common patterns:
- **Type errors / missing imports** — straightforward code fix
- **Signing errors** — set `CODE_SIGNING_ALLOWED=NO` in the build command for simulator builds
- **Missing dependencies** — check if a Swift Package needs resolving (`xcodebuild -resolvePackageDependencies`)

### 3. Find the .app

Skip if the build script already installed and launched the app.

Locate the built `.app` bundle:
1. Check build output for the path — xcodebuild prints it, and scripts often echo it
2. If not found, search DerivedData:
   ```bash
   find ~/Library/Developer/Xcode/DerivedData -name "*.app" -path "*/Debug-iphonesimulator/*" -newer /tmp/.build_start_marker -maxdepth 6 2>/dev/null
   ```
   (Create `/tmp/.build_start_marker` with `touch` before the first build to filter stale bundles.)
3. Fallback — search the working directory: `find . build/ output/ -name "*.app" -maxdepth 4 2>/dev/null`

If multiple results, pick the newest by modification time.

### 4. Boot Simulator and Launch

Skip if the build script already handled this.

```bash
xcrun simctl boot "iPhone 16" 2>/dev/null || true
open -a Simulator
```

Extract the bundle ID from the app:
```bash
/usr/libexec/PlistBuddy -c "Print :CFBundleIdentifier" /path/to/Built.app/Info.plist
```

Install with `mcp__iosef__install_app`, then launch with `mcp__iosef__launch_app` (set `terminate_running: true` to ensure a clean start).

**Wait for the app to render.** After launch, pause 2-3 seconds before inspecting — SwiftUI apps often show a blank frame initially while the view hierarchy loads. If the first screenshot is blank or shows only a launch screen, wait another 2 seconds and retry once before treating it as a bug.

### 5. Inspect and Test

Take a screenshot (`mcp__iosef__view`) and read the accessibility tree (`mcp__iosef__describe`).

**If there's a test description:** plan the interaction sequence, then execute step by step using iosef tools — `tap`, `type`, `swipe`, `find`, `wait`, `exists`. Screenshot after each significant action so you can trace what happened if something goes wrong.

**If no test description:** do a general walkthrough — screenshot the initial state, tap the main interactive elements, verify the app responds sensibly, and report what you find.

**Handling permission dialogs:** iOS may show system alerts (location, notifications, camera, etc.) that block your app's UI. If `mcp__iosef__describe` shows a system alert, either tap "Allow" / "Don't Allow" as appropriate for the test, or dismiss it. Don't mistake a permission dialog for a bug in the app.

### 6. On Failure

1. **Diagnose first.** Compare what you expected vs. what you see. Check `mcp__iosef__log_show` for crash logs or assertion failures — filter by the app's bundle ID or process name to cut through noise.
2. **Read the relevant source**, then fix the code. If elements are hard to target, add `.accessibilityIdentifier("descriptive-id")` to key views — this is a legitimate fix, not a refactor.
3. **Rebuild and retest** (go back to step 2).

**Iteration limits:** You have up to 5 build-test-fix cycles. If you're not making progress by iteration 3, step back and reconsider — are you fixing symptoms instead of the root cause? On iteration 5, if the issue persists, stop and report to the user: what you've tried, what the current state is, and your best theory on what's wrong.

### 7. On Success

Take a final screenshot as evidence. Report:
- The final state of the app (screenshot)
- A summary of any fixes you made (files changed, what was wrong, what you did)
- If no fixes were needed, just confirm the app works as expected

## Rules

- **Verify after every fix** — rebuild and retest, never assume a fix worked.
- **Minimum changes only** — fix the issue, don't refactor surrounding code.
- **Read before writing** — always read a file before modifying it.
- **Don't modify the build system** unless the build error specifically requires it.
- **Screenshot after every significant state change** — screenshots are your evidence trail. If something goes wrong later, you can trace back through them to understand what happened.

---
> Source: [webcoyote/AppTestCircuit](https://github.com/webcoyote/AppTestCircuit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
