---
name: mobile-e2e
description: End-to-end debugging on physical iOS device using mobile-mcp. Use when debugging UI issues, testing interactions, or automating device actions. Use when this capability is needed.
metadata:
  author: thomasttvo
---

# Mobile E2E Debugging

## Core Workflow

```
1. Save screenshot    → mcp__mobile__mobile_save_screenshot saveTo: /tmp/screen.png
2. Resize             → sips -Z 400 /tmp/screen.png -o /tmp/screen_small.png 2>/dev/null
3. Read               → Read tool on /tmp/screen_small.png
4. Need to tap?
   YES → omni.py     → python3 ~/.claude/skills/mobile-e2e/omni.py /tmp/screen.png --screen-width 390 --screen-height 844
       → tap         → mcp__mobile__mobile_click_on_screen_at_coordinates
   NO  → done
```

**NEVER tap without screenshot + omni first. No guessing coordinates.**

## Quick Reference

**Diana bundle IDs**: Dev: `ai.openspace.Capture.DEV` | Prod: `ai.openspace.Capture`

**Launch/reload app**:
```
mcp__mobile__mobile_terminate_app device: UDID, packageName: BUNDLE_ID
mcp__mobile__mobile_launch_app device: UDID, packageName: BUNDLE_ID
```

**Testing fast refresh**: Change colors/padding, NOT i18n strings (cached)

## Setup

```bash
~/.claude/skills/mobile-e2e/setup.sh --launch        # Interactive setup + launch app
~/.claude/skills/mobile-e2e/setup.sh --skip-build    # Skip WDA build if already installed
```

## Troubleshooting

**WDA not responding**:
```bash
pgrep -f "ios tunnel" || ios tunnel start --userspace &
pkill -f "ios forward 8100"; ios forward 8100 8100 --udid DEVICE_UDID &
curl http://localhost:8100/status
```

**App crashes**: Check Metro (`curl http://localhost:8082/status`), revert recent changes, or `yarn clean && yarn install && yarn pod`

**OmniParser cold start**: First call ~30-60s while Replicate spins up. Be patient.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thomasttvo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
