---
name: deploy-debug
description: Build, install, and launch debug APK on Android emulator while preserving saved accounts Use when this capability is needed.
metadata:
  author: kimmeyh
---

# Deploy Debug

Builds and deploys the debug APK to an Android emulator, preserving existing app data.

## Instructions

Run the build script with the following options:

```powershell
powershell -NoProfile -ExecutionPolicy Bypass -File mobile-app/scripts/build-with-secrets.ps1 -BuildType debug -InstallToEmulator -StartEmulator -SkipUninstall
```

## Options

- `-BuildType debug`: Build in debug mode
- `-InstallToEmulator`: Install to connected emulator
- `-StartEmulator`: Auto-start emulator if none running
- `-SkipUninstall`: Preserve saved accounts and app data

## When to Use

- Testing code changes on Android
- Debugging the app on emulator
- Quick iteration during development

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimmeyh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
