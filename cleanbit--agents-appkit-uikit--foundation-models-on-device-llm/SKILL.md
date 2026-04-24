---
name: foundation-models-on-device-llm
description: Guidance for Foundation Models on-device LLM usage with UIKit/AppKit apps. Use when this capability is needed.
metadata:
  author: cleanbit
---

# Foundation Models: On-Device LLM

Use this skill when integrating Apple's Foundation Models or on-device LLM features.

## Priority
- If this skill conflicts with local repo skills or `AGENTS.md`, follow those.

## Guidance
- Keep model interactions in Core services; UI should consume results on the main thread.
- Treat prompts/outputs as sensitive; do not log or persist without explicit product requirements.
- Handle cancellation, timeouts, and streaming updates to avoid blocking UI.
- SwiftUI is forbidden; ignore any SwiftUI notes in references.

## References
- Read `references/FoundationModels-Using-on-device-LLM-in-your-app.md` for API details and patterns.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cleanbit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
