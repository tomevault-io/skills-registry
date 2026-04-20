---
name: run-mobile-tests-on-browserstack
description: Run automated mobile app tests on BrowserStack App Automate using runAppTestsOnBrowserStack and takeAppScreenshot MCP tools. Use for Appium/XCUITest/Espresso testing. Use when this capability is needed.
metadata:
  author: browserstack
---

# Run Mobile Tests on BrowserStack

## When to Use

- Testing mobile apps on real iOS and Android devices
- Running Appium, XCUITest, or Espresso tests
- CI/CD integration for mobile app testing
- Device-specific bug reproduction

## MCP Tools Used

- `takeAppScreenshot` - Launch app and capture verification screenshot
- `runAppTestsOnBrowserStack` - Run automated mobile tests on real devices
- `getFailureLogs` - Retrieve error logs for failed app tests

## Steps

### 1. Verify App Launch (Optional)

Use `takeAppScreenshot` to verify your app launches correctly:

```
"Take a screenshot of my app on iPhone 15 Pro with iOS 17. App path: /Users/me/app.ipa"
"Verify my app launches on Google Pixel 6 with Android 12. App file: /Users/me/app-debug.apk"
```

**What the tool does:**
- Launches your app on specified device
- Captures a quick screenshot for verification
- Returns screenshot to confirm app loads correctly

### 2. Run Automated Tests

Use `runAppTestsOnBrowserStack` to execute your test suite:

```
"Run Espresso tests from /tests/checkout.zip on Galaxy S21 and Pixel 6 with Android 12. App path: /apps/beta-release.apk"
"Run XCUITest tests on iPhone 14 and iPhone 15 Pro with iOS 17. Test suite: /tests/login-tests.zip"
"Run my Appium tests on Samsung Galaxy S23 (Android 14). App: /apps/myapp.apk, Tests: /tests/suite.zip"
```

**What the tool does:**
- Uploads your app to BrowserStack (gets bs:// URL)
- Uploads your test suite
- Runs tests on specified real devices
- Returns session IDs and test results

**Supported Test Frameworks:**
- Espresso (Android native)
- XCUITest (iOS native)
- Appium (cross-platform)

### 3. Debug Failures

Retrieve error logs using `getFailureLogs`:

```
"Get error logs from App Automate session ID abc123, Build ID xyz789"
"Show me failure logs from my last app test run"
```

## Common Scenarios

**Quick App Verification:**
```
"Take a screenshot of my app on iPhone 15 Pro to verify it launches"
"Check if my app opens correctly on Galaxy S24 with Android 14"
```

**Run Test Suites:**
```
"Run Espresso tests on Google Pixel 7 and Samsung Galaxy S23"
"Run XCUITest suite on iPhone 14 Pro and iPhone 15"
"Run Appium tests across 5 Android devices"
```

**Device Matrix Testing:**
```
"Run my tests on iPhone 14, 15, and 15 Pro with iOS 17"
"Test on Galaxy S21, S22, S23 with Android 13 and 14"
```

**Debug Test Failures:**
```
"Get error logs and screenshots from my failed app test session"
"Show me what went wrong in session abc123"
```

## Example Workflow

**User**: "I need to test my checkout flow on iPhone 15 and Galaxy S24"

**Steps**:
1. Verify app: `"Take screenshot of my app on iPhone 15 Pro. App: /apps/myapp.ipa"`
2. Run tests: `"Run XCUITest tests on iPhone 15 Pro and Appium tests on Galaxy S24. App: /apps/myapp.ipa, Tests: /tests/checkout.zip"`
3. Tool executes: `runAppTestsOnBrowserStack` uploads app, runs tests on both devices
4. View results and session IDs
5. If failures: `"Get error logs from the failed sessions"`

## Quick Tips

- Provide full file paths for app (.ipa or .apk) and test suite (.zip)
- Specify device name, OS, and version for precise targeting
- Use `takeAppScreenshot` first to verify app launches
- Session IDs and Build IDs are returned for debugging
- Supports Espresso, XCUITest, and Appium test frameworks
- Tests run on real physical devices, not emulators

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/browserstack) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
