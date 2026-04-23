---
name: app-start-readiness-policy
description: Guidelines for coordinating splash screen dismissal with background data readiness Use when this capability is needed.
metadata:
  author: aarshps
---

# App Start Readiness Policy

To prevent "Welcome" state flashes and UI jumps, the app must remain on the splash screen until the initial data payload is fully synchronized and processed.

## 1. Splash Screen Condition
Use the AndroidX `SplashScreen` API to hold the screen:
```kotlin
splashScreen.setKeepOnScreenCondition { 
    !isAuthSuccessful || !isDataLoaded 
}
```

## 2. Readiness Flags
- `isAuthSuccessful`: Set after Biometric or Firebase Auth succeeds.
- `isDataLoaded`: Set ONLY after the first valid data snapshot is processed (e.g., `calculateHeroData` finishes).

## 3. Safety Timeout (Failsafe)
Always implement a 5-second `postDelayed` fallback in `initializeApp` to ensure the app never hangs indefinitely due to network or Firestore latency:
```kotlin
mainNestedScrollView.postDelayed({
    if (!isDataLoaded) {
        isDataLoaded = true
    }
}, 5000)
```

## 4. Initialization Order (CRITICAL)
NEVER call background tasks or safety timeouts before your views are fully initialized. This prevents `UninitializedPropertyAccessException`.
1. `setContentView`
2. `findViewById` (All)
3. `auth/viewModel` Init
4. `safetyTimeout` (Last)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aarshps) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
