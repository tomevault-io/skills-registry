---
name: infographic-weather
description: Generate a TV-style weather infographic with a location-specific seasonal background. Use when the user asks for a visual weather forecast or a weather infographic for a specific address. Use when this capability is needed.
metadata:
  author: openclaw
---

# Infographic Weather

Generate a professional TV-style weather broadcast frame using Gemini 3 Pro Image (Nano Banana).

## Features
- **Seasonal Backgrounds**: Generates a photorealistic backdrop based on the address and current local season (hemisphere-aware).
- **Real-time Data**: Pulls live weather and 7-day forecast from Open-Meteo.
- **Broadcast UI**: Stitches data and background into a professional TV broadcast layout.

## Usage

```bash
python3 {baseDir}/scripts/generate_infographic.py --address "10 Downing St, London" --lat 51.5033 --lon -0.1276 --output "out/london-weather.png"
```

## Environment
- `GEMINI_API_KEY`: Required for image generation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
