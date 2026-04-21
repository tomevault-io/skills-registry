---
name: context7-cli
description: Fetch up-to-date library documentation from Context7. Use for getting code examples and API docs for any library. Use when this capability is needed.
metadata:
  author: brittonr
---

# Usage

```bash
# Search for libraries
context7-cli search react "how to use hooks"

# Get documentation (use library ID from search)
context7-cli docs /vercel/next.js "middleware authentication"

# With specific version
context7-cli docs /vercel/next.js/v14.3.0 "app router"

# JSON output
context7-cli search --json react "hooks" | jq '.results[0].id'
context7-cli docs --json /vercel/next.js "routing"

# With explicit API key
context7-cli -k "ctx7sk_xxx" search react "hooks"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brittonr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
