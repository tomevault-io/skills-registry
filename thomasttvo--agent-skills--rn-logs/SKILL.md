---
name: rn-logs
description: Check React Native console logs from the running app. Use when debugging RN app behavior or when user asks to check logs. Use when this capability is needed.
metadata:
  author: thomasttvo
---

# RN Logs Skill

Check React Native console logs from the running Diana app.

## Steps

1. Determine which Metro port your app is using (default: 8081, but could be 8082, 8083, etc.)

2. Check if the log collector is running for that port:
   ```bash
   pgrep -f "rn-logs.js.*--port=8081"
   ```

3. If not running, start it with the correct port:
   ```bash
   node ~/.claude/skills/rn-logs/rn-logs.js --port=8081 &
   ```

4. Read recent logs (use the port-specific file):
   ```bash
   tail -50 /tmp/rn-logs-8081.txt
   ```

5. To search for specific logs:
   ```bash
   grep "pattern" /tmp/rn-logs-8081.txt
   ```

## Log Format

```
[timestamp] [type] message
```

Types: `log`, `warn`, `error`, `info`

## Notes

- Each Metro port gets its own log file: `/tmp/rn-logs-{PORT}.txt`
- The collector auto-reconnects when the app reloads
- Default port is 8081 if not specified
- Script location: `~/.claude/skills/rn-logs/rn-logs.js`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thomasttvo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
