---
name: lang-go
description: Go conventions and reliability checklist for agents. Use when this capability is needed.
metadata:
  author: t3chn
---

# Go Conventions

- Use `gofmt`.
- Prefer `context.Context` threading; enforce timeouts for external calls.
- Use table-driven tests; run `go test ./...`.
- Keep packages small; avoid cyclic deps.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t3chn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
