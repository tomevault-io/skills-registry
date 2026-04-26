---
name: maui-speech-to-text
description: > Use when this capability is needed.
metadata:
  author: davidortinau
---

# Speech-to-Text — Gotchas & Best Practices

For full service implementation, types, and UI integration patterns, see `references/speech-to-text-api.md`.

## Critical: Permission Handling

Always request permissions **before** starting speech recognition. Both microphone and speech permissions are required.

```csharp
// ❌ Starting recognition without checking permissions
var result = await _speechService.StartListeningAsync();

// ✅ Always check permissions first
if (!await _speechService.RequestPermissionsAsync())
    return; // Gracefully handle denial
var result = await _speechService.StartListeningAsync(cancellationToken);
```

### ⚠️ iOS Requires Both Permission Descriptions

Missing either `NSSpeechRecognitionUsageDescription` or `NSMicrophoneUsageDescription` in Info.plist will cause a **runtime crash** — not a graceful failure.

### ⚠️ Android: Permission Re-prompt

On Android, if the user denies the `RECORD_AUDIO` permission twice, the OS stops showing the prompt. You must guide users to Settings manually.

## Common Mistakes

### ❌ No Timeout — Indefinite Listening

Always set a timeout to prevent indefinite listening sessions that drain battery:

```csharp
// ❌ No timeout — listens forever if no speech detected
await _speechToText.StartListenAsync(options, CancellationToken.None);

// ✅ Use a combined timeout + user cancellation token
using var timeoutCts = new CancellationTokenSource(TimeSpan.FromSeconds(60));
using var combinedCts = CancellationTokenSource.CreateLinkedTokenSource(
    userCancellationToken, timeoutCts.Token);
await _speechToText.StartListenAsync(options, combinedCts.Token);
```

### ❌ Not Unsubscribing Event Handlers

Leaking event subscriptions causes duplicate processing and memory leaks:

```csharp
// ❌ Subscribe without unsubscribe
_speechToText.RecognitionResultUpdated += OnRecognitionResultUpdated;

// ✅ Always unsubscribe in finally block
try
{
    _speechToText.RecognitionResultUpdated += OnRecognitionResultUpdated;
    // ... listen ...
}
finally
{
    _speechToText.RecognitionResultUpdated -= OnRecognitionResultUpdated;
}
```

### ❌ Not Disposing CancellationTokenSource

```csharp
// ❌ Leaked CTS
_currentCts = new CancellationTokenSource();

// ✅ Dispose in finally
try { /* ... */ }
finally
{
    _currentCts?.Dispose();
    _currentCts = null;
}
```

## Platform Pitfalls

| Platform | Pitfall |
|----------|---------|
| iOS | Missing either plist key → **runtime crash** |
| Android | User denies permission twice → OS stops prompting; must redirect to Settings |
| All | No timeout → battery drain from indefinite listening |
| All | Calling `StartListeningAsync` while already listening → returns error, not exception |

## Architecture Tips

1. **Wrap `ISpeechToText` in a service** — Don't use `SpeechToText.Default` directly in ViewModels. Wrap in `ISpeechRecognitionService` for testability and state management.

2. **Use partial results for UX** — Subscribe to `PartialResultReceived` for live transcription feedback. Users expect to see words appear as they speak.

3. **Continuous listening = loop with delay** — Loop `StartListeningAsync` with small delays (`Task.Delay(100)`) for conversation mode.

4. **Guard against double-start** — Check state before starting:
   ```csharp
   if (State == SpeechRecognitionState.Listening)
       return new SpeechRecognitionResultDto { Success = false, ErrorMessage = "Already listening" };
   ```

5. **Natural language output** — CommunityToolkit.Maui returns normalized, punctuated text — not raw phonemes. No post-processing needed for basic use cases.

6. **UI-agnostic service** — The `ISpeechRecognitionService` pattern works identically with XAML/MVVM, C# Markup, and MauiReactor. See `references/speech-to-text-api.md` for all three patterns.

## Checklist

- [ ] `CommunityToolkit.Maui` NuGet installed (look up current version)
- [ ] `UseMauiCommunityToolkit()` called in `MauiProgram.cs`
- [ ] `ISpeechToText` registered as singleton via DI
- [ ] iOS: Both `NSSpeechRecognitionUsageDescription` and `NSMicrophoneUsageDescription` in Info.plist
- [ ] Android: `RECORD_AUDIO` permission in AndroidManifest.xml
- [ ] Permissions checked before every `StartListeningAsync` call
- [ ] Timeout configured (recommend 60 seconds max)
- [ ] Event handlers unsubscribed in `finally` blocks
- [ ] `CancellationTokenSource` disposed after use

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidortinau) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
