---
name: pressto-button
description: Tells you how to use the pressto button in react-native/expo apps Use when this capability is needed.
metadata:
  author: neversight
---

Usage

```tsx
import { PressableScale, PressableOpacity } from "pressto";

// Scales down on press
<PressableScale onPress={() => {}}>
  <Text>Scale</Text>
</PressableScale>

// Fades when pressed
<PressableOpacity onPress={() => {}}>
  <Text>Opacity</Text>
</PressableOpacity>
```

Supports standard Pressable props.

# pressto-button

For tap targets needing 60fps visual feedback without JS thread blocking.

Instructions

1. Import PressableScale (physical feedback) or PressableOpacity (subtle fade)
2. Wrap content and add onPress handler

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
