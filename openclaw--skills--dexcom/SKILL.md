---
name: dexcom
description: Monitor blood glucose via Dexcom G7/G6 CGM Use when this capability is needed.
metadata:
  author: openclaw
---

# Dexcom CGM

Real-time blood glucose monitoring via Dexcom G6/G7 continuous glucose monitor.

## Setup

Set environment variables:
```bash
export DEXCOM_USER="your@email.com"
export DEXCOM_PASSWORD="your-password"
export DEXCOM_REGION="ous"  # or "us" (optional, defaults to "ous")
```

Or configure in `~/.clawdbot/clawdbot.json`:
```json5
{
  skills: {
    "dexcom": {
      env: {
        DEXCOM_USER: "your@email.com",
        DEXCOM_PASSWORD: "your-password",
        DEXCOM_REGION: "ous"
      }
    }
  }
}
```

## Usage

**Formatted report:**
```bash
uv run {baseDir}/scripts/glucose.py now
```

**Raw JSON:**
```bash
uv run {baseDir}/scripts/glucose.py json
```

## Example Output

```
🩸 Glucose: 100 mg/dL (5.6 mmol/L)
📈 Trend: steady ➡️
🎯 Status: 🟢 In range
⏰ 2026-01-18 09:30:00
```

## Requirements

- Dexcom G6 or G7 with Share enabled
- uv (Python package manager)
- Valid Dexcom Share credentials

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
