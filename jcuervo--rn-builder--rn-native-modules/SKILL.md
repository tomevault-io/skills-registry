---
name: rn-native-modules
description: Bridge native iOS (Swift) and Android (Kotlin) code into Expo React Native apps. Prefer the Expo Modules API for new modules; fall back to React Native Turbo Modules only when Expo Modules cannot express the requirement. Apply when wrapping a native SDK, exposing platform APIs, or writing a config plugin. Use when this capability is needed.
metadata:
  author: jcuervo
---

# rn-native-modules

## Purpose

When the JavaScript layer cannot do what the app needs (access a platform API, wrap a vendor SDK, run synchronous native logic), this skill owns the bridge — written cleanly enough that the rest of the team can read it without becoming Swift/Kotlin specialists.

## When to Apply

Use this skill when the task is:

- Wrapping a native vendor SDK (e.g., Stripe Terminal, MapKit, ML Kit, a custom enterprise SDK)
- Exposing a platform API not yet available in Expo modules (a new iOS framework, an Android system service)
- Writing a **config plugin** that modifies `Info.plist`, `AndroidManifest.xml`, or other native files during prebuild
- Patching an existing native module's behavior

Do **not** use this skill for:

- Anything an off-the-shelf Expo module already does (always check first)
- Pure-JS libraries (don't reach for native unless you need native)
- Brownfield host-app integration → that's the brownfield migration skill

## Decision Tree

```
Need native functionality
   │
   ├─ Is there an existing Expo module (npm: @expo/*, expo-*)?
   │     ├─ Yes → Use it. Stop here.
   │     └─ No → continue
   │
   ├─ Is there a community RN package (well-maintained, recent commits)?
   │     ├─ Yes → Use it. Add a config plugin if it needs native config.
   │     └─ No → continue
   │
   ├─ Do you only need to modify native config (Info.plist, manifest, build.gradle)?
   │     ├─ Yes → Write a config plugin. See prebuild-config-plugins.md.
   │     └─ No → continue
   │
   ├─ Do you need to expose new native logic to JS?
   │     ├─ Yes → Write an Expo Module. See expo-modules-api-first.md.
   │     │       (Falls back to Turbo Module only if Expo Modules can't express the API shape.)
   │     └─ No → reconsider why you're in this skill
```

## Problem → Reference

| Task | Read |
|---|---|
| Expo Modules API — preferred bridge for new modules | [expo-modules-api-first.md](references/expo-modules-api-first.md) |
| Turbo Module on iOS (fallback when Expo Modules can't fit) | [turbo-module-ios.md](references/turbo-module-ios.md) |
| Turbo Module on Android | [turbo-module-android.md](references/turbo-module-android.md) |
| Config plugin to modify native files during prebuild | [prebuild-config-plugins.md](references/prebuild-config-plugins.md) |

## Default Choice

**Expo Modules API.** Reasons:

- Single TypeScript-first API that compiles to both Swift and Kotlin sides.
- Auto-generates the JS interface; no manual codegen.
- First-class TypeScript types, no `any`.
- Integrates with `prebuild` and EAS Build out of the box.
- Works on the new React Native architecture (Fabric + Turbo Modules) under the hood.

Use Turbo Modules directly only when:

- The module needs to ship outside Expo's ecosystem (community package consumed by bare RN users too).
- You need synchronous return values from the JS thread that Expo Modules cannot express.
- You're contributing to a project that already standardized on Turbo Modules.

## Performance Reference

For native module performance (threading model, sync vs async, JSI overhead):

> Read `~/.claude/skills/react-native-best-practices/references/native-turbo-modules.md` and `native-threading-model.md` before writing a hot-path module. Do not invent perf advice.

## Critical Rules

- **One module per concern.** A vendor SDK wrapper, a platform API, a config patch — each is its own module.
- **Always async by default.** Sync native calls block the JS thread. See the perf reference above.
- **Test on both platforms before merging.** A module that only works on iOS is half a module.
- **Document the prebuild step.** Any change to a config plugin or native code requires `npx expo prebuild --clean` plus a dev client rebuild. Put it in the PR description.
- **No private APIs.** App Store reviewers grep IPAs for private symbol references.
- **Match the host's deployment target.** Adding a module that bumps `IPHONEOS_DEPLOYMENT_TARGET` silently breaks installs on older devices.

## Verification

After writing a module:

1. `npx expo prebuild --clean` succeeds without warnings.
2. `npx expo run:ios` and `npx expo run:android` build cleanly.
3. JS-side smoke test from a dev screen: call each exported method, log the result.
4. Type-check passes (`npx tsc --noEmit`) with no `any`.
5. Component test for the JS wrapper using mocked native side (see `../rn-testing/references/mocking-native-modules.md`).

---
> Source: [jcuervo/rn-builder](https://github.com/jcuervo/rn-builder) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
