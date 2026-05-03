---
name: fix-testflight-crashes
description: Download crash reports from TestFlight via App Store Connect API and analyze/fix crashes in iOS apps. Use when this capability is needed.
metadata:
  author: stgarrity
---

# Fix TestFlight Crashes

This skill fetches crash reports from App Store Connect and helps fix them.

## Step 1: Fetch Crash Data

Run the crash fetcher script to get the latest crash diagnostics:

```bash
python .claude/skills/fix-testflight-crashes/scripts/fetch_crashes.py
```

**Note**: Requires `PyJWT`, `cryptography`, and `requests` packages.

## Step 2: Analyze Crashes

The script will output:
- All crash submissions with stack traces
- Build-by-build crash counts

Look for patterns in the stack traces:
1. **App code frames** - Look for your app's binary name in the backtrace
2. **Exception type** - EXC_CRASH (SIGABRT), EXC_BAD_ACCESS, etc.
3. **Termination reason** - Often contains the actual error message

## Step 3: Locate the Code

Use the stack trace to find the relevant code:
- Look for your app's class and method names
- Search for the crash signature in your codebase

## Step 4: Common Crash Patterns

### UICollectionView / List Crashes
```
_Bug_Detected_In_Client_Of_UICollectionView_Invalid_Number_Of_Items_In_Section
```
**Cause**: Race condition where data changes during list updates (e.g., polling + delete)
**Fix**: Block data refresh during mutations:
```swift
private var isPerformingMutation = false

func deleteItem() async {
    isPerformingMutation = true
    defer { isPerformingMutation = false }
    // ... delete logic
}

func refreshData() async {
    guard !isPerformingMutation else { return }
    // ... refresh logic
}
```

### Force Unwrap Crashes
```swift
// Bad - will crash if nil
let value = optionalValue!

// Good - safe unwrapping
guard let value = optionalValue else { return }
```

### Main Thread Violations
```swift
// Bad - UI update from background
DispatchQueue.global().async {
    self.label.text = "Updated"  // CRASH
}

// Good - dispatch to main
DispatchQueue.global().async {
    DispatchQueue.main.async {
        self.label.text = "Updated"
    }
}
```

### Array Index Out of Bounds
```swift
// Bad
let item = array[index]  // CRASH if index >= array.count

// Good
guard index < array.count else { return }
let item = array[index]
```

### SwiftUI View Lifecycle Issues
- Use `@StateObject` for ViewModels in navigation destinations, not `@ObservedObject`
- Never use `.constant()` for alert bindings
- Filter cancellation errors when setting `errorMessage`

## Configuration

Create a `.env` file in your project root (copy from `.env.example`):

```bash
APPSTORE_ISSUER_ID=your-issuer-id
APPSTORE_KEY_ID=your-key-id
APPSTORE_KEY_PATH=~/Desktop/AuthKey_XXXXXXXX.p8
APPSTORE_APP_ID=1234567890
APPSTORE_APP_NAME=My App
```

The API key must have **Admin** role for crash access.

## API Endpoints Used

- `GET /v1/apps/{id}/betaFeedbackCrashSubmissions` - List crash submissions
- `GET /v1/betaFeedbackCrashSubmissions/{id}/crashLog` - Get crash log text
- `GET /v1/builds/{id}/metrics/betaBuildUsages` - Get crash counts per build

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stgarrity) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
