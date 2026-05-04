---
name: app-screenshots
description: | Use when this capability is needed.
metadata:
  author: neversight
---

## What This Does
- Configure `Snapfile` for app routes, devices, locales.
- Run `fastlane snapshot` across simulators/emulators.
- Apply device frames via `fastlane frameit`.
- Organize output by platform/locale/device.

## Why fastlane (not Screenshots Pro)
- Free, CLI-first, CI/CD native, handles all device sizes automatically.

## Prerequisites
- `gem install fastlane`
- Xcode (iOS) or Android SDK (Android)

## Usage
- `/app-screenshots apps/mobile for ios in english and spanish`
- `/app-screenshots . for iphone 15 pro only`

## References
- `references/fastlane-config.md`
- `references/device-specs.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
