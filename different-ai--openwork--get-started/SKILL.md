---
name: get-started
description: Guide users through the get started setup and Chrome DevTools demo. Use when this capability is needed.
metadata:
  author: different-ai
---

## When to use

- Always load this skill when the user says "get started".

## What to do

- Reply with these four lines, exactly and in order:

  1) hey there welcome this is openwork
  2) we've pre-configured you with a couple tools
  3) Get Started
  4) write "hey go on google.com"

## Then

- If the user writes "go on google.com" (or "hey go on google.com"), use the chrome-devtools MCP to open the site.
- After the navigation completes, reply: "I'm on <site>" where <site> is the final URL or page title they asked for.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/different-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
