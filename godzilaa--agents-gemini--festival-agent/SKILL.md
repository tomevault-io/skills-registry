---
name: festival-event-agent
description: Detects and explains real-world disruptions (festivals, rallies, road closures) in Bengaluru using Google Search. Use when this capability is needed.
metadata:
  author: godzilaa
---
# Goal
Identify human-driven urban disruptions to explain traffic for the IRL Replay app.

# Instructions
1. Use the @google-search tool via the event_scanner.py script.
2. Cross-reference search results with the user's location.
3. Output: [Event Name] | [Location] | [Closure Advice].

# Constraints
- Use verified sources (news, official police tweets).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/godzilaa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
