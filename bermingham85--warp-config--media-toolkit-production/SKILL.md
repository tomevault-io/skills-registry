---
name: media-toolkit-production
description: Voice generation and audio mixing using ElevenLabs API v3 with intelligent timing optimization. Trigger when user mentions voice generation, TTS, ElevenLabs, audio production, or script format CHARACTER (emotion) dialogue. Use when this capability is needed.
metadata:
  author: bermingham85
---

# Media Toolkit Production

## Instructions

### Critical Requirements (ALL MUST BE ENFORCED)

1. **Accent Persistence** - Every ElevenLabs API call MUST include accent descriptor:
   ```
   text: "[Irish Midlands accent] Stay close, Jess."
   ```

2. **Clip Reuse from History** - Before generating any clip:
   - Check ElevenLabs History API
   - If matching text+voice exists → reuse it
   - Log: "Reusing from history: [id]"

3. **Re-Timing Workflow** - Local timing adjustment, NO regeneration:
   - Adjust downstream timing based on actual durations
   - Zero API calls for timing fixes

4. **Zero Voice Overlaps** (PRIORITY 1) - Voices NEVER overlap unless intentional

5. **Line Refinement** - User approval loop:
   - User: "Line 2 more panicked"
   - Adjust: stability=0.3, style=0.8, text="[panicked, breathless]..."
   - Archive both original and rework versions

### Script Format
```
CHARACTER (emotion): dialogue text
[SFX: description, duration: 5s, volume: 0.8]
```

### Production Workflow
1. Load character database (Notion)
2. Parse script (dialogue + SFX)
3. Generate timeline
4. Detect overlaps
5. Generate audio (v3 with history reuse)
6. Optimize timing
7. Mix audio (FFmpeg)

## DO NOT
- Generate audio without checking history first
- Omit accent descriptors from any API call
- Allow voice overlaps without explicit marking
- Regenerate clips for timing adjustments

## DO
- Always include accent in text prompt
- Check history → archive → then generate if needed
- Preserve both original and reworked versions
- Track and report token usage efficiency

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bermingham85) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
