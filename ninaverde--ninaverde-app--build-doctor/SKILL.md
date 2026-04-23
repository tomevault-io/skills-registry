---
name: build-doctor
description: Automates the "Hard Restart" workflow (clean -> pub get -> build) and diagnoses common errors.
metadata:
  author: ninaverde
---

# Build Doctor: Automated Self-Healing

## The "Hard Restart" Protocol
When the user says "fix the build", "hard reset", or "clean everything":

1.  **Execute**:
    ```bash
    flutter clean
    flutter pub get
    flutter build apk --debug
    ```

2.  **Diagnose**:
    -   **Gradle Errors**: Check `android/build.gradle` for Kotlin version mismatches.
    -   **Pod Errors**: (If iOS) Check `ios/Podfile.lock`.
    -   **Pub Get Errors**: Check for version conflicts in `pubspec.yaml`.

## Common Cures
-   **"Client ID" Error**: Check `google-services.json` and Firebase Console.
-   **"Multidex" Error**: Ensure `multiDexEnabled true` in `android/app/build.gradle`.
-   **"Null Pointer"**: Search for `!` bangs and replace with `?` null-aware operators.

## Verification
-   Do not mark as "Fixed" until the app launches successfully on the connected device.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ninaverde) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
