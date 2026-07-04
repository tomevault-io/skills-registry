---
name: rebuild-to-fix-lib-import-errors
description: When typecheck finds an error importing from a lib in the monorepo, rebuild the lib Use when this capability is needed.
metadata:
  author: LedgerHQ
---

When typecheck finds an error importing from a lib in the monorepo rebuild the lib using the command below:

```shell
nx run @ledgerhq/name_of_lib_here:build
```

## Example

### `live-countervalues-react` has been updated and the import in `live-common` is erroring

We run `typecheck` and get the following error:

> src/currencies/hooks.ts:1:10 - error TS2305: Module '"@ledgerhq/live-countervalues-react"' has no exported member 'useMarketcapIds'.
> 1 import { useMarketcapIds } from "@ledgerhq/live-countervalues-react";

We need to rebuild `@ledgerhq/live-countervalues-react`

```shell
nx run @ledgerhq/live-countervalues-react:build
```

---
> Source: [LedgerHQ/ledger-live](https://github.com/LedgerHQ/ledger-live) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-04 -->
