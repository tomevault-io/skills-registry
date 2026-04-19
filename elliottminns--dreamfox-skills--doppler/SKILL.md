---
name: doppler
description: Fetch secrets from Doppler for API keys and credentials. Use when this capability is needed.
metadata:
  author: elliottminns
---

# doppler

Use `doppler` to fetch secrets. Requires `DOPPLER_TOKEN` env var (service token scoped to project).

## Setup
1. Create project in Doppler dashboard
2. Add secrets (GEMINI_API_KEY, BREX_TOKEN, etc.)
3. Generate service token for project/config
4. Set `DOPPLER_TOKEN` on server

## Common commands

Get a single secret:
```bash
doppler secrets get GEMINI_API_KEY --plain
```

List all secrets (names only):
```bash
doppler secrets --only-names
```

List secrets with values:
```bash
doppler secrets
```

Run a command with secrets injected:
```bash
doppler run -- some-command
```

Download secrets as JSON:
```bash
doppler secrets download --no-file --format json
```

## Notes
- Service tokens are scoped to a single project + config (e.g., `dev`, `prod`)
- `--plain` strips quotes/newlines for scripting
- Never log or echo secret values
- Prefer `doppler secrets get <NAME> --plain` for single secrets

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/elliottminns) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
