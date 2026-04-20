---
name: prospect-snapshot-builder
description: This skill generates a Bullhorn-ready data block from a LinkedIn URL. Use when this capability is needed.
metadata:
  author: jessie-rgb
---

# Stratice Prospect Snapshot Builder

## Goal
Create a condensed, structured summary of a potential hiring manager or candidate for quick review or Bullhorn entry.

## Instructions
1. Scrape or analyze the provided `linkedin_url`.
2. Summarize their current role, tenure, and key responsibilities.
3. Identify their "Career Trajectory" (e.g., rapidly promoted, stable industry veteran).
4. Extract 3 "Conversation Starters" based on their profile.
5. Format the output as a clean Markdown list suitable for Bullhorn notes.

## Inputs
- `linkedin_url`: The prospect's LinkedIn profile.
- `additional_context`: Any specific info you already know.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jessie-rgb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
