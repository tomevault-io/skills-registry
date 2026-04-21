---
name: roomshare-db-migrations
description: name: roomshare-db-migrations Use when this capability is needed.
metadata:
  author: suryateja-byte
---
---
name: roomshare-db-migrations
description: Use for schema changes, indexes, RLS, migrations, and rollbacks. Must produce safe migration + rollback notes.
---

# DB Migration SOP

- Provide: schema diff, migration steps, rollback steps
- Prefer additive changes first (expand/contract)
- Add indexes for any high-cardinality filters
- For RLS: include test queries for allowed/denied access

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/suryateja-byte) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
