---
name: authensor-gateway
description: Fail-safe policy gate for OpenClaw marketplace skills. Use when this capability is needed.
metadata:
  author: demerzels-lab
---

# Authensor Gateway

This skill is a lightweight gateway that adds policy checks and receipts to OpenClaw marketplace actions. Low-risk actions run automatically. High-risk actions require approval. Known-dangerous actions are blocked.

## Setup
1. Get a demo key: https://forms.gle/QdfeWAr2G4pc8GxQA
2. Add the env vars in `~/.openclaw/openclaw.json`:

```json5
{
  skills: {
    entries: {
      "authensor-gateway": {
        enabled: true,
        env: {
          CONTROL_PLANE_URL: "https://authensor-control-plane.onrender.com",
          AUTHENSOR_API_KEY: "authensor_demo_..."
        }
      }
    }
  }
}
```

## Notes
- This is a hosted beta. No local database or server is required.
- Full setup guide: https://github.com/AUTHENSOR/Authensor-for-OpenClaw

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/demerzels-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
