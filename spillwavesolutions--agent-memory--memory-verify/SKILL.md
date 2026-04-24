---
name: memory-verify
description: | Use when this capability is needed.
metadata:
  author: spillwavesolutions
---

# Memory Verify Skill

Provide verification commands for an existing agent-memory installation. This
skill never runs commands automatically.

## When to Use

- User wants to confirm install/config works
- User wants to validate daemon health
- User wants to confirm event ingestion and hooks

## When Not to Use

- Installation (use `memory-install`)
- Configuration changes (use `memory-configure`)
- Troubleshooting failures (use `memory-troubleshoot`)

## Wizard Flow

### Step 1: Choose what to verify

```
What would you like to verify?

1. Installation (binaries in PATH)
2. Configuration file
3. Daemon health
4. Event ingestion
5. Hooks configuration
6. Full verification (all of the above)

Enter selection [1-6]:
```

### Step 2: Provide verification commands

**Installation**

```
memory-daemon --version
memory-ingest --version
```

**Configuration**

```
cat ~/.config/agent-memory/config.toml
```

**Daemon health**

```
memory-daemon status
memory-daemon query --endpoint http://[::1]:50051 root
```

**Event ingestion**

```
echo '{"hook_event_name":"SessionStart","session_id":"verify"}' | memory-ingest
memory-daemon query --endpoint http://[::1]:50051 root
```

**Hooks configuration**

```
grep -n "memory-ingest" ~/.claude/hooks.yaml
```

### Step 3: Interpret results

Provide simple guidance based on outcomes:

- If `command not found`: advise PATH setup or reinstall
- If `status` shows stopped: suggest `memory-daemon start`
- If no events: confirm hooks file and daemon endpoint

## Notes

- These are optional verification commands
- Do not execute commands automatically
- For failures, switch to `memory-troubleshoot`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spillwavesolutions) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
