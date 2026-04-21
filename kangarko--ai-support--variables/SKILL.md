---
name: variables
description: Troubleshooting variable/placeholder resolution, custom JavaScript variables, and PAPI integration Use when this capability is needed.
metadata:
  author: kangarko
---

# Variables Troubleshooting

## Common Mistakes

- **`SenderCache.isDatabaseLoaded()` prerequisite** — placeholders return empty if the database hasn't finished loading for that player. Common on join with slow databases
- **`{player_*}` defaults to sender context** — in most contexts, `{player_prefix}` = sender's prefix. Use `{receiver_prefix}` for receiver-specific values
- **Nested PAPI variables work** — `{player_prefix}` reads Vault's PAPI output. Complex nesting is supported
- **Custom variable JavaScript testable via `/chc script`** — use this to debug JavaScript conditions in `variables/*.yml` files

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kangarko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
