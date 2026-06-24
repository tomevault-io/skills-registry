---
name: maui-custom-handlers
description: > Use when this capability is needed.
metadata:
  author: davidortinau
---

# .NET MAUI Custom Handlers

## Decision: Customize Existing vs. Create New

| Scenario | Approach |
|---|---|
| Change how a built-in control looks/behaves on one platform | **Customize** — use `AppendToMapping` / `PrependToMapping` |
| Need the change on only some instances of a control | **Customize** — subclass the control + type-check in mapper |
| Need a completely new cross-platform control with native backing | **Create new** handler with partial classes |

> ⚠️ **Prefer `AppendToMapping`** over `ModifyMapping`. `ModifyMapping` replaces
> the default mapper action entirely — if the framework adds behaviour in a
> future release, your override silently drops it.

---

## Gotchas & Common Mistakes

### Mapper customizations are global

Every instance of the control is affected. Guard with a subclass check for
instance-specific behaviour:

```csharp
// ❌ Removes borders from EVERY Entry in the app
EntryHandler.Mapper.AppendToMapping("NoBorder", (handler, view) =>
{
#if ANDROID
    handler.PlatformView.Background = null;
#endif
});

// ✅ Only affects BorderlessEntry instances
EntryHandler.Mapper.AppendToMapping("NoBorder", (handler, view) =>
{
    if (view is not BorderlessEntry) return;
#if ANDROID
    handler.PlatformView.Background = null;
#endif
});
```

### Unsubscribe native events in `HandlerChanging`

Failing to remove native event handlers causes **memory leaks** because the
native view may outlive the managed wrapper.

```csharp
// ❌ Subscribes but never unsubscribes — leaks
entry.HandlerChanged += (s, e) =>
{
#if ANDROID
    ((Entry)s!).Handler!.PlatformView.As<Android.Widget.EditText>()!
        .FocusChange += OnNativeFocusChange;
#endif
};

// ✅ Pair subscribe in HandlerChanged with unsubscribe in HandlerChanging
entry.HandlerChanged += OnHandlerChanged;
entry.HandlerChanging += OnHandlerChanging;
```

### Partial class name/namespace mismatch

Namespace and class name **must match exactly** across the shared handler file
and every platform file. A mismatch silently creates separate classes — no
compiler error, just a handler that does nothing on that platform.

### Conditional `using` placement

The `using PlatformView = ...` aliases must be at the **top of the shared
handler file** (not the platform files) so the `ViewHandler<TControl, TPlatformView>`
base-class generic resolves correctly per platform.

```csharp
// ✅ Top of Handlers/VideoPlayerHandler.cs
#if ANDROID
using PlatformView = Android.Widget.VideoView;
#elif IOS || MACCATALYST
using PlatformView = AVKit.AVPlayerViewController;
#elif WINDOWS
using PlatformView = Microsoft.UI.Xaml.Controls.MediaPlayerElement;
#endif
```

### Missing `CreatePlatformView()`

Each platform partial **must** override `CreatePlatformView()`. Omitting it
produces a compile error — but the error message points at the base class,
not your handler, making it confusing to debug.

---

## Mapper Method Selection

| Method | Risk | Use when |
|---|---|---|
| `AppendToMapping` | Low — runs after default | Adding behaviour without breaking defaults |
| `PrependToMapping` | Low — runs before default | Setting initial state that the default can override |
| `ModifyMapping` | ⚠️ High — replaces default | You intentionally want to suppress the framework's mapper logic |

---

## PropertyMapper vs. CommandMapper

| Mapper | Purpose | Pattern |
|---|---|---|
| `PropertyMapper` | Sync a bindable property to the native view | Runs whenever the property value changes |
| `CommandMapper` | Fire-and-forget action from control → handler | Runs once per invocation, no return value |

> ⚠️ Don't put property sync logic in `CommandMapper` — it won't re-run when
> the property changes, leading to stale native views.

---

## Checklist — New Handler

- [ ] Cross-platform control inherits `View` (or appropriate base)
- [ ] Shared handler file has conditional `using PlatformView = ...` aliases
- [ ] Handler inherits `ViewHandler<TControl, PlatformView>`
- [ ] `PropertyMapper` maps every bindable property
- [ ] Each platform partial overrides `CreatePlatformView()`
- [ ] Namespace + class name identical across all partial files
- [ ] Handler registered in `MauiProgram.cs` via `ConfigureMauiHandlers`
- [ ] Native event subscriptions cleaned up in `HandlerChanging`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidortinau) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
