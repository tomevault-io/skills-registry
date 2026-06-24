---
name: flutter-platform
description: Flutter platform integrations skill hub: permissions, camera/files, push notifications, background tasks, platform channels, app lifecycle, and foldables considerations. Use when this capability is needed.
metadata:
  author: melsaeed276
---

# Platform Skills

## What this domain covers

Platform integrations and constraints: permissions, camera/files, push notifications, background tasks, platform channels, app lifecycle, and foldables platform considerations.

## When to read it

- A feature depends on OS capabilities or app lifecycle.
- You need to bridge native APIs via platform channels.
- You’re targeting foldables and need posture/limitations context.

## Subtopics

- Permissions: [permissions.md](./permissions.md)
- Camera & files: [camera_files.md](./camera_files.md)
- Push notifications: [push_notifications.md](./push_notifications.md)
- Background tasks: [background_tasks.md](./background_tasks.md)
- Platform channels: [platform_channels.md](./platform_channels.md)
- App lifecycle: [app_lifecycle.md](./app_lifecycle.md)
- Foldables: [foldables.md](./foldables.md)

## Decision guide

- If you need **lifecycle correctness**, go to [app_lifecycle.md](./app_lifecycle.md).
- If you need **native bridging**, go to [platform_channels.md](./platform_channels.md).
- If you need **foldable posture constraints**, go to [foldables.md](./foldables.md) (and then [../ui/foldables.md](../ui/foldables.md)).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melsaeed276) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
