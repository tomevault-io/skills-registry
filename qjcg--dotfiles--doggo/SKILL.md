---
name: doggo
description: DNS command-line client Use when this capability is needed.
metadata:
  author: qjcg
---

# Doggo DNS lookup

## When to use this skill

Use this skill whenever the user asks for a DNS lookup.

## Install

- If the `doggo` tool is already in the user's PATH, don't install it.
- If the tool is already available via `go tool doggo`, don't install it.
- Otherwise, if needed, install doggo via `go install github.com/mr-karan/doggo/cmd/doggo@latest`

## DNS lookup

```bash
doggo example.com  # or whatever query
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qjcg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
