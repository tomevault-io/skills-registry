---
name: maui-platform-invoke
description: > Use when this capability is needed.
metadata:
  author: davidortinau
---

# Platform Invoke — Gotchas & Best Practices

## Decision Framework

| Scenario | Approach |
|---|---|
| 1–5 lines, one-off check | `#if ANDROID` conditional compilation |
| Service with logic, testable | Partial classes in `Platforms/` folders |
| Swappable implementations, mocking | Interface + DI registration |
| Team prefers `*.android.cs` naming | Custom file patterns in `.csproj` |

**Default choice: partial classes + interface + DI.** Only use `#if` for trivial inline checks.

## Common Mistakes

### 1. Overusing `#if` directives

```csharp
// ❌ Complex logic buried in #if blocks — untestable, hard to read
public async Task<bool> CheckConnectivity()
{
#if ANDROID
    // 30 lines of Android networking code...
#elif IOS
    // 25 lines of iOS networking code...
#endif
}

// ✅ Use partial classes — each platform file is clean and testable
// Services/ConnectivityService.cs (shared)
public partial class ConnectivityService
{
    public partial Task<bool> CheckConnectivityAsync();
}
// Platforms/Android/Services/ConnectivityService.cs
public partial class ConnectivityService
{
    public partial Task<bool> CheckConnectivityAsync() { /* Android impl */ }
}
```

### 2. Mismatched namespaces in partial classes

All partial class files **must** use the same namespace, or they become separate classes.

```csharp
// ❌ Different namespaces — creates TWO unrelated classes
// Services/MyService.cs
namespace MyApp.Services;
public partial class MyService { }

// Platforms/Android/MyService.cs
namespace MyApp.Platforms.Android;  // WRONG!
public partial class MyService { }

// ✅ Same namespace everywhere
namespace MyApp.Services;
public partial class MyService { }
```

### 3. Depending on concrete classes in shared code

```csharp
// ❌ Shared code depends on concrete platform type — can't mock in tests
public class MyViewModel
{
    readonly DeviceOrientationService _service = new();
}

// ✅ Depend on interface — enables unit testing and swapping
public class MyViewModel
{
    readonly IDeviceOrientationService _service;
    public MyViewModel(IDeviceOrientationService service) => _service = service;
}
```

### 4. Forgetting the `#else` fallback

```csharp
// ❌ Fails compilation on unsupported platforms
public string GetDeviceName()
{
#if ANDROID
    return Android.OS.Build.Model;
#elif IOS
    return UIKit.UIDevice.CurrentDevice.Name;
#endif  // No return for Windows or other platforms!
}

// ✅ Always include a fallback
#else
    return "Unknown";
#endif
```

## Platform Pitfalls

### ⚠️ Android: Platform.CurrentActivity can be null

`Platform.CurrentActivity` is `null` before `OnCreate` completes or when the app is in the background. Always null-check or throw a clear exception.

### ⚠️ MSBuild auto-includes `Platforms/{Platform}/` files

Files under `Platforms/Android/` are only compiled for Android — no `#if` needed.
But if you put platform code in a shared folder (e.g., `Services/`), you **must** use `#if` or conditional `<Compile>` items.

### ⚠️ Custom file patterns need explicit MSBuild conditions

If using `*.android.cs` naming, files won't auto-include. Add `<Compile>` items with platform conditions in your `.csproj`.

## Checklist

- [ ] Partial classes share the **same namespace** across all files
- [ ] Complex platform code uses partial classes, not `#if` blocks
- [ ] Shared code depends on **interfaces**, not concrete implementations
- [ ] Interfaces registered in DI in `MauiProgram.cs`
- [ ] `#if` blocks include `#else` fallback for unsupported platforms
- [ ] `Platform.CurrentActivity` null-checked before use (Android)
- [ ] Custom file patterns (if used) have MSBuild `<Compile>` conditions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidortinau) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
