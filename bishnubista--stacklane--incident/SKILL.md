---
name: incident
description: Run incident triage and mitigation with deterministic logging and optional rollback via the configured deploy adapter. Use when this capability is needed.
metadata:
  author: bishnubista
---

# /stacklane:incident

Use the script entrypoint to create incident artifacts and execute controlled mitigation.

## Command

```bash
bash "${CLAUDE_PLUGIN_ROOT}/scripts/stacklane-incident.sh" --environment production --name incident
```

## Optional Flags

- `--name <short-name>`
- `--environment <name>`
- `--service <name>`
- `--severity <SEV0|SEV1|SEV2|SEV3>`
- `--mitigation <none|rollback>`
- `--rollback-target <deployment-id>`
- `--confirm-production I_UNDERSTAND_PRODUCTION` (required for production rollback)
- `--healthcheck-url <url>`
- `--dry-run`

## Execution Rules

1. Always create an incident record in `.stacklane/incidents/`.
2. Always collect logs unless no logs command is configured.
3. If `--mitigation rollback` is requested in production, require confirmation token.
4. If `--healthcheck-url` is provided, it must be an absolute `http://` or `https://` URL.
5. Return the incident document path and any rollback result.

## Boundaries

- Focus on containment and recovery actions.
- Keep broader process follow-ups in plangate.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bishnubista) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
