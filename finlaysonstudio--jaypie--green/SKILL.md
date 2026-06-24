---
name: green
description: Completion criteria -- typecheck, build, test, format must all pass Use when this capability is needed.
metadata:
  author: finlaysonstudio
---

# Green

Before considering work complete, verify affected packages are "green":

```bash
npm run typecheck -w packages/<name>    # or workspaces/<name>
npm run build -w packages/<name>        # or workspaces/<name>
npm test -w packages/<name>             # or workspaces/<name>
npm run format packages/<name>          # or workspaces/<name>
```

All four must pass with zero errors or warnings for each affected package.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/finlaysonstudio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
