---
name: m3e-haptic-standards
description: Guidelines for premium tactile "Felt" experience in Varisankya Use when this capability is needed.
metadata:
  author: aarshps
---

# M3E Haptic Standards

To achieve the "Premium Felt" experience, haptics must be curated based on the interaction weight:

## Interaction Hierarchy

| Interaction | Haptic Role | Method |
|-------------|-------------|--------|
| **Scrolling** | Mechanical Tick | `PreferenceHelper.attachScrollHaptics(recyclerView)` |
| **Success/Save** | Confirmation | `PreferenceHelper.performSuccessHaptic(view)` |
| **Error/Reject** | Reject | `PreferenceHelper.performErrorHaptic(view)` |
| **Toggle/Click** | Light Tick | `PreferenceHelper.performClickHaptic(view)` |
| **Long Press** | Contextual | `HapticFeedbackConstants.LONG_PRESS` |

## Implementation Patterns

### 1. Mechanical Scroll Feel
Attach to ANY list containing core data:
```kotlin
PreferenceHelper.attachScrollHaptics(recyclerView)
```

### 2. Guarded Haptics
NEVER call `performHapticFeedback` directly in Activities. Use `PreferenceHelper` to ensure the user's preference is respected:
```kotlin
saveButton.setOnClickListener {
    PreferenceHelper.performSuccessHaptic(it)
    // ... save logic ...
}
```

### 3. Selection Haptics
For AutoCompleteTextViews or Bottom Sheets, trigger a tick on every delta/selection:
```kotlin
PreferenceHelper.performHaptics(v, HapticFeedbackConstants.SEGMENT_TICK)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aarshps) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
