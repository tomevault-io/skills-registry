---
name: forgood-heartbeat
description: name: forgood-heartbeat Use when this capability is needed.
metadata:
  author: ahnjinyoung
---
---
name: forgood-heartbeat
version: 0.1.0
description: Periodic heartbeat for FORGOOD agent. Checks for missions needing attention, posts updates, and monitors treasury health.
metadata: {"openclaw":{"emoji":"💓","category":"blockchain","requires":{"env":["FORGOOD_API_URL"]}}}
---

# FORGOOD Heartbeat

This is your periodic check-in routine. Run every 4+ hours (or when idle).

## Heartbeat Checklist

### 1. Check for missions needing evaluation

```bash
curl -s "${FORGOOD_API_URL}/missions?status=proposed&limit=10"
```

If there are `proposed` missions:
- Notify the user: "🧠 {count} missions waiting for evaluation"
- Offer to auto-evaluate them

### 2. Check for proofs needing verification

```bash
curl -s "${FORGOOD_API_URL}/missions?status=proof_submitted&limit=10"
```

If there are `proof_submitted` missions:
- Notify the user: "👁️ {count} proofs waiting for verification"
- Offer to auto-verify them

### 3. Check for verified missions needing reward

```bash
curl -s "${FORGOOD_API_URL}/missions?status=verified&limit=10"
```

If there are `verified` missions:
- Notify the user: "💰 {count} verified missions ready for reward payout"
- ⚠️ Do NOT auto-reward — always ask for confirmation

### 4. Check treasury health

```bash
curl -s "${FORGOOD_API_URL}/treasury"
```

- If balance < 100 FORGOOD (100000000000000000000 wei): Warn "⚠️ Treasury running low!"
- Otherwise: Report balance casually

### 5. API health check

```bash
curl -s "${FORGOOD_API_URL}/health"
```

If the API is down, alert the user immediately.

### 6. Post to Moltbook (if noteworthy activity)

If there were completed missions since last heartbeat, compose a Moltbook post using the `forgood-social` skill.

## Summary Format

```
💓 FORGOOD Heartbeat

🏦 Treasury: {balance} FORGOOD
📋 Proposed: {count} | Active: {count} | Verified: {count} | Rewarded: {count}

{any action items}
```

## Frequency

- Check every 4+ hours when running as daemon
- Also run when user says "check status" or "what's happening"
- Track lastHeartbeatCheck timestamp to avoid over-checking

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ahnjinyoung) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
