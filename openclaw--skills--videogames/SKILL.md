---
name: videogames
description: Use when working with a skill to lookup video game information, prices, compatibility, and duration.
metadata:
  author: openclaw
---

# Video Game Skill 🎮

This skill allows OpenClaw to search for games, view Steam details, check ProtonDB compatibility, estimate playtime with HowLongToBeat, and find the best prices using CheapShark.

## Tools

### `scripts/game_tool.py`

This Python script interacts with multiple game APIs (Steam, CheapShark, ProtonDB).

**Usage:**

1.  **Search for deals (CheapShark):**
    ```bash
    python3 scripts/game_tool.py deals "Game Name"
    ```

2.  **Check Compatibility (ProtonDB):**
    ```bash
    python3 scripts/game_tool.py compatibility <APPID>
    ```

3.  **Get Game Duration (HLTB):**
    ```bash
    python3 scripts/game_tool.py duration "Game Name"
    ```

4.  **View details & Specs (Steam):**
    ```bash
    python3 scripts/game_tool.py details <APPID>
    ```

## Notes
- The script requires Python 3.
- No external library installation required.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
