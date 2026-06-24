---
name: kmp-datastore-development
description: Builds, tests, and formats KMP DataStore multiplatform library. Handles Android, iOS, and Desktop (JVM) platforms.
metadata:
  author: parkwoocheol
---

# KMP DataStore Development Workflow

This skill automates building, testing, and formatting for the KMP DataStore multiplatform library.

## Quick Commands

### Format Code

```bash
./gradlew spotlessApply
```

### Run All Tests

```bash
./gradlew allTests
```

### Build All Platforms

```bash
./gradlew build
```

## Platform-Specific Workflows

### Android

```bash
# Build
./gradlew :kmp-datastore:assembleDebug

# Test
./gradlew :kmp-datastore:testDebugUnitTest
```

### iOS

```bash
# Build
./gradlew :kmp-datastore:linkIosSimulatorArm64
```

### Desktop (JVM)

```bash
# Build
./gradlew :kmp-datastore:desktopMainClasses

# Test
./gradlew :kmp-datastore:desktopTest
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/parkwoocheol) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
