---
name: maui-app-lifecycle
description: > Use when this capability is needed.
metadata:
  author: davidortinau
---

# .NET MAUI App Lifecycle

## Critical Behavioral Gotchas

### ⚠️ Resumed ≠ first launch

`Resumed` only fires when returning from the `Stopped` state. On first launch the sequence is `Created` → `Activated` — `Resumed` is **never** called.

```csharp
// ❌ Putting initialization logic in OnResumed — won't run on first launch
protected override void OnResumed()
{
    LoadUserProfile();  // Skipped on cold start!
}

// ✅ Use OnActivated for logic that must run on every foreground entry
protected override void OnActivated()
{
    LoadUserProfile();  // Runs on both first launch and resume
}
```

### ⚠️ Deactivated ≠ Stopped

A dialog, split-screen, or notification pull-down triggers `Deactivated` **without** `Stopped`. Don't save heavy state on `Deactivated` — the app may never actually background.

```csharp
// ❌ Heavy save on every deactivation — fires too often
protected override void OnDeactivated()
{
    await SaveAllDataToDatabase();  // Wasteful for a dialog appearance
}

// ✅ Save state only when truly backgrounded
protected override void OnStopped()
{
    await SaveAllDataToDatabase();
}
```

### ⚠️ Android back button skips Stopped

On Android, pressing the hardware back button may call `Destroying` **without** `Stopped` if the activity finishes. Critical save logic in `OnStopped` alone can be missed.

```csharp
// ✅ Save in both Stopped and Destroying for safety on Android
protected override void OnStopped()
{
    base.OnStopped();
    SaveDraft();
}

protected override void OnDestroying()
{
    base.OnDestroying();
    SaveDraft();  // Catches Android back-button finish
}
```

### ⚠️ Multiple windows fire independently

On iPad, Mac Catalyst, and desktop Windows, each `Window` instance fires its own lifecycle events independently. Don't assume a single global lifecycle.

## Performance: Keep Handlers Fast

Long-running work in lifecycle handlers causes **ANR kills on Android** (5s timeout) and **watchdog kills on iOS** (limited background time).

```csharp
// ❌ Blocking the lifecycle handler
protected override void OnStopped()
{
    Thread.Sleep(3000);  // ANR on Android!
    SaveData();
}

// ✅ Fire-and-forget or use a brief async save
protected override void OnStopped()
{
    base.OnStopped();
    Preferences.Set("draft_text", _viewModel.DraftText);  // Fast, synchronous
}
```

For larger state, use `SecureStorage` or file-based serialization — but keep it under 1–2 seconds.

## iOS Scene Lifecycle

iOS 13+ uses the scene lifecycle (`SceneWillConnect`, etc.). Older delegate methods are still forwarded by MAUI, but you should target scene-based APIs for modern iOS.

## State Preservation Checklist

- [ ] Transient UI state (scroll position, draft text) saved in `OnStopped`
- [ ] State restored in `OnResumed` — not in `OnActivated` (avoid double-restore)
- [ ] No heavy I/O in `OnDeactivated` — it fires too frequently
- [ ] Android: critical save logic also in `OnDestroying` (back-button case)
- [ ] Lifecycle handlers complete in under 2 seconds
- [ ] Multi-window apps handle per-window state independently

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidortinau) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
