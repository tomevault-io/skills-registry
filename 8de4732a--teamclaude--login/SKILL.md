---
name: login
description: Login to TeamClaude server to get a CLI token for plugin authentication Use when this capability is needed.
metadata:
  author: 8de4732a
---

Run the TeamClaude login script to authenticate this CLI plugin with the server.

The script will:
1. Start a temporary local HTTP server
2. Open the browser for Auth0 login
3. Save the received JWT token to `~/.teamclaude/token`

Execute:
```bash
node "${CLAUDE_PLUGIN_ROOT}/scripts/login.mjs" $ARGUMENTS
```

After login succeeds, confirm to the user that authentication is complete and the plugin will use the saved token automatically for all future requests. Only `SIDECAR_API_BASE_URL` environment variable is needed going forward.

If the user hasn't set `SIDECAR_API_BASE_URL`, remind them to add it to their shell profile:
```bash
export SIDECAR_API_BASE_URL="https://your-server.com"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/8de4732a) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
