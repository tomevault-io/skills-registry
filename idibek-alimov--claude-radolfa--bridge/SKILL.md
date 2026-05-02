---
name: bridge
description: Synchronize Java Domain Records to TypeScript Interfaces. Use when this capability is needed.
metadata:
  author: idibek-alimov
---
**ultrathink**
1. Scan `tj.radolfa.domain` for Java Records.
2. For each Record, generate a corresponding `types.ts` interface in `frontend/src/entities/[domain-name]/model/`.
3. Handle type mapping: `String` -> `string`, `BigDecimal` -> `number`, `List` -> `[]`.
4. Add a comment: `// AUTO-GENERATED FROM BACKEND - DO NOT EDIT`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/idibek-alimov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
