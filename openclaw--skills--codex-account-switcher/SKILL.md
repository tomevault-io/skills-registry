---
name: codex-account-switcher
description: > Use when this capability is needed.
metadata:
  author: openclaw
---

# Codex Account Switcher

Manage multiple OpenAI Codex identities (e.g. personal, family, work) by swapping the authentication token file. Includes smart auto-selection based on quota budget scoring.

## Usage

### List Accounts
```bash
python3 {baseDir}/scripts/codex-accounts.py list
python3 {baseDir}/scripts/codex-accounts.py list --verbose
python3 {baseDir}/scripts/codex-accounts.py list --json
```

### Add an Account
Interactive wizard — starts a fresh browser login (`codex logout && codex login`) so you explicitly choose the identity to capture. Press **Enter** to accept the default name (local-part of the email).

```bash
python3 {baseDir}/scripts/codex-accounts.py add
```

### Switch Account
Instantly swap the active login. Syncs **all** account tokens to OpenClaw.

```bash
python3 {baseDir}/scripts/codex-accounts.py use oliver
```

### Auto-Switch to Best Quota
Probes each account for current quota, scores them, and switches to the best one.

```bash
python3 {baseDir}/scripts/codex-accounts.py auto
python3 {baseDir}/scripts/codex-accounts.py auto --json
```

Example output:
```
Account         7d    5h   Score      7d Resets      5h Resets
──────────── ───── ───── ─────── ────────────── ──────────────
oliver         60%    1%   +12.0   Apr 03 08:08      in 4h 40m ←
elise          62%   75%   +25.3   Apr 03 10:15      in 2h 01m
sylvia         MAX    0%   +51.8   Apr 03 07:51      in 5h 00m
```

### Sync All Profiles
Push all saved account tokens to OpenClaw (useful after manual token refresh).

```bash
python3 {baseDir}/scripts/codex-accounts.py sync
```

## Auto Mode — How It Works

### 1. Quota Probing

For each saved account, `auto` temporarily switches `~/.codex/auth.json` and runs `codex exec` with a prompt that embeds the account's **user_id** in a JSON block:

```
Quota-Probe for {"user_id": "user-UtCmyIUOTxc4D1OHV1e5Ibew"} — Only reply OK
```

This creates a Codex session that returns `rate_limits` data (primary/5h and secondary/weekly windows). The embedded user_id makes sessions attributable to the exact account for downstream quota tracking.

### 2. Budget-Based Scoring

The ideal usage pace is 100% spread evenly over 7 days. At any point in the week, the **budget** is where usage *should* be:

```
budget = (elapsed_hours / 168) × 100%
```

The **score** measures how far ahead or behind budget an account is:

```
score = (actual_weekly% - budget%) + daily_penalty
```

- **Negative score** = under budget (good — has headroom)
- **Positive score** = over budget (burning too fast)
- **Lowest score wins**

### 3. 5-Hour Penalty

The 5h window can block you even with weekly headroom. Penalties prevent picking an account that's about to hit the wall:

| 5h Usage | Penalty | Reason |
|----------|---------|--------|
| < 75% | 0 | Fine |
| 75–89% | +10 | Getting warm |
| 90–99% | +50 | About to be blocked |
| 100% | +200 | Blocked right now |

### 4. Example

Three accounts, 5 days into the weekly window:

| Account | Weekly | Budget | Δ | 5h | Penalty | Score |
|---------|--------|--------|---|-----|---------|-------|
| Oliver | 60% | 71% | -11 | 1% | 0 | **-11** ← best |
| Elise | 62% | 69% | -7 | 75% | +10 | **+3** |
| Sylvia | 100% | 71% | +29 | 0% | 0 | **+29** |

Oliver wins: most headroom relative to pace, and 5h is clear.

## OpenClaw Integration

### Token Sync

Every `use`, `auto`, or `sync` command syncs **all** saved account tokens to **all** OpenClaw agents' `auth-profiles.json`:

- Profile key format: `openai-codex:oliver@drobnik.com` (email extracted from JWT)
- Old name-based keys (e.g. `openai-codex:oliver`) are migrated automatically
- Each profile includes: `type`, `provider`, `access`, `refresh`, `expires`, `accountId`, `email`
- Also updates each agent's `auth.json` (for the active account)

This allows OpenClaw to auto-switch between Codex accounts internally without going through `~/.codex/auth.json`.

### Account Activity Log

Every account switch is logged to `~/.codex/account-activity.jsonl`:

```json
{"timestamp": 1774878000, "account": "oliver", "user_id": "user-UtCmyIUOTxc4D1OHV1e5Ibew"}
```

This enables the [quota-dashboard](../quota-dashboard/) skill to attribute Codex Desktop session rate_limit data to the correct account, since session files don't record which user created them.

## Setup

See [SETUP.md](SETUP.md) for prerequisites and setup instructions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
