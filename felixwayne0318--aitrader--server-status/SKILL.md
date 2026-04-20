---
name: server-status
description: | Use when this capability is needed.
metadata:
  author: felixwayne0318
---

# Server Status Check

## Server Information

| Item | Value |
|------|-------|
| **IP** | 139.180.157.152 |
| **User** | linuxuser |
| **Service** | nautilus-trader |
| **Path** | /home/linuxuser/nautilus_AlgVex |

## Check Commands

### Service Status
```bash
sudo systemctl status nautilus-trader
```

### View Logs
```bash
# Last 50 lines
sudo journalctl -u nautilus-trader -n 50 --no-hostname

# Real-time follow
sudo journalctl -u nautilus-trader -f --no-hostname
```

### Check Processes
```bash
ps aux | grep main_live.py
```

## Status Indicators

### ✅ Normal Operation
```
🚀 *Strategy Started*
📊 *Instrument*: BTCUSDT-PERP
Active: active (running)
```

### ❌ Common Errors

| Error | Cause | Solution |
|-------|-------|----------|
| `can't open file 'main.py'` | Wrong entry file | Change ExecStart to `main_live.py` |
| `EOFError: EOF when reading a line` | Missing env var | Add `Environment=AUTO_CONFIRM=true` |
| `telegram.error.Conflict` | Telegram conflict | Does not affect trading, can ignore |

## Quick Diagnosis

If service is abnormal, check in this order:

1. **Service Status**: `sudo systemctl status nautilus-trader`
2. **Recent Logs**: `sudo journalctl -u nautilus-trader -n 100 --no-hostname`
3. **Config File**: `cat /etc/systemd/system/nautilus-trader.service`
4. **Entry File**: Confirm it's `main_live.py`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/felixwayne0318) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
