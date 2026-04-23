---
name: debug
description: Debug NanoClaw Codex container issues: auth, mounts, session resumption, runtime failures, and service health. Use when this capability is needed.
metadata:
  author: shalomobongo
---

# NanoClaw Debug (Codex Runtime)

## Architecture Snapshot

```
Host (macOS/Linux)                     Container (Linux VM)
────────────────────────────────────────────────────────────
src/container-runner.ts                container/agent-runner/
    │                                      │
    │ spawns container runtime             │ runs Codex SDK
    │                                      │
    ├── data/env/env  ───────────────> /workspace/env-dir/env
    ├── groups/{folder} ─────────────> /workspace/group
    ├── data/ipc/{folder} ──────────> /workspace/ipc
    ├── data/sessions/{folder}/.codex/ -> /home/node/.codex/
    └── (main only) project root ----> /workspace/project
```

## Key Logs

- Host logs: `logs/nanoclaw.log`
- Host error logs: `logs/nanoclaw.error.log`
- Per-run container logs: `groups/{folder}/logs/container-*.log`
- Per-group Codex session/auth state: `data/sessions/{folder}/.codex/`

## Fast Health Check

```bash
launchctl list | grep nanoclaw
container ls --format '{{.Names}} {{.Status}}' 2>/dev/null | grep nanoclaw
grep -E 'ERROR|WARN|timeout|failed' logs/nanoclaw.log | tail -30
```

## Common Failures

### 1) "Codex process exited with code 1"

Check latest container log first:

```bash
ls -lt groups/*/logs/container-*.log | head -5
cat groups/<group>/logs/container-<timestamp>.log
```

Likely causes:
- Missing auth (`codex login` not done, no API key in `.env`)
- Runtime missing (`container`/Docker unavailable)
- Session mount mismatch

### 2) Auth Not Available in Container

Supported auth paths:
- Host Codex login copied into `data/sessions/{group}/.codex/auth.json`
- API keys from `.env` allowlist (`OPENAI_API_KEY`, `CODEX_API_KEY`, plus optional OpenAI overrides)

Checks:

```bash
ls -la data/sessions/<group>/.codex/
grep -E '^(OPENAI_API_KEY|CODEX_API_KEY|OPENAI_BASE_URL|OPENAI_ORG_ID|OPENAI_PROJECT_ID)=' .env
```

Validate mounted env inside container:

```bash
echo '{}' | container run -i \
  --mount type=bind,source=$(pwd)/data/env,target=/workspace/env-dir,readonly \
  --entrypoint /bin/bash nanoclaw-agent:latest \
  -c 'export $(cat /workspace/env-dir/env | xargs); env | grep -E "^(OPENAI_API_KEY|CODEX_API_KEY|OPENAI_BASE_URL|OPENAI_ORG_ID|OPENAI_PROJECT_ID)="'
```

### 3) Session Not Resuming

Ensure mount target is `.codex` and session IDs are persisted:

```bash
grep -n '/home/node/.codex' src/container-runner.ts
sqlite3 store/messages.db "SELECT group_folder, session_id FROM sessions ORDER BY group_folder;"
ls -la data/sessions/<group>/.codex/sessions 2>/dev/null
```

### 4) Mount Issues

```bash
grep -E 'mount|Mount validated|REJECTED|Container mount configuration' logs/nanoclaw.log | tail -30
cat ~/.config/nanoclaw/mount-allowlist.json
```

Remember:
- Read-write uses `-v host:container`
- Read-only uses `--mount type=bind,...,readonly`

### 5) WhatsApp Not Responding

```bash
grep -E 'Connected to WhatsApp|Connection closed|QR|authentication required' logs/nanoclaw.log | tail -20
grep -E 'New messages|Processing messages|Spawning container|Agent error' logs/nanoclaw.log | tail -40
sqlite3 store/messages.db "SELECT chat_jid, MAX(timestamp) latest FROM messages GROUP BY chat_jid ORDER BY latest DESC LIMIT 10;"
```

## Manual Runtime Test

```bash
mkdir -p data/env groups/test
cp .env data/env/env

echo '{"prompt":"Say hello","groupFolder":"test","chatJid":"test@g.us","isMain":false}' | \
  container run -i \
  --mount "type=bind,source=$(pwd)/data/env,target=/workspace/env-dir,readonly" \
  -v $(pwd)/groups/test:/workspace/group \
  -v $(pwd)/data/ipc/test:/workspace/ipc \
  nanoclaw-agent:latest
```

## Rebuild and Restart

```bash
npm run build
./container/build.sh
launchctl kickstart -k gui/$(id -u)/com.nanoclaw
```

## If Still Broken

Collect and share:
- Last 100 lines of `logs/nanoclaw.log`
- Latest `groups/<folder>/logs/container-*.log`
- `sqlite3 store/messages.db "SELECT * FROM sessions;"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shalomobongo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
