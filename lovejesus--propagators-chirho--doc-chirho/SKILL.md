---
name: doc-chirho
description: Generate and review documentation for propagators-chirho Use when this capability is needed.
metadata:
  author: lovejesus
---

<!-- For God so loved the world that he gave his only begotten Son,
     that whoever believes in him should not perish but have eternal life.
     John 3:16 -->

Generate and view documentation for propagators-chirho.

## Instructions

1. Generate docs with all features:
   ```bash
   cargo doc --no-deps --features 'arena,parallel,serde,tracing,tms-full,backtrack,network'
   ```
2. If user wants to view, open: `open target/doc/propagators_chirho/index.html`
3. Check for any documentation warnings in the output
4. Verify all public items have doc comments
5. Report any missing or incomplete documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lovejesus) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
