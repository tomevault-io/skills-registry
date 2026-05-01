---
name: revolut
description: Revolut web automation via Playwright: login/logout, list accounts, and fetch transactions. Use when this capability is needed.
metadata:
  author: openclaw
---

# Revolut Banking Automation

Fetch current account balances, investment portfolio holdings, and transactions for all wallet currencies and depots in JSON format. Uses Playwright to automate Revolut web banking.

**Entry point:** `{baseDir}/scripts/revolut.py`

## Setup

See [SETUP.md](SETUP.md) for prerequisites and setup instructions.

## Commands

```bash
python3 {baseDir}/scripts/revolut.py --user oliver login
python3 {baseDir}/scripts/revolut.py --user oliver accounts
python3 {baseDir}/scripts/revolut.py --user oliver transactions --from YYYY-MM-DD --until YYYY-MM-DD
python3 {baseDir}/scripts/revolut.py --user sylvia portfolio
python3 {baseDir}/scripts/revolut.py --user oliver invest-transactions --from YYYY-MM-DD --until YYYY-MM-DD
```

## Recommended Flow

```
login → accounts → transactions → portfolio → logout
```

Always call `logout` after completing all operations to delete the stored browser session.

## Notes
- Per-user state stored in `{workspace}/revolut/` (deleted by `logout`).
- Output paths (`--out`) are sandboxed to workspace or `/tmp`.
- No `.env` file loading — credentials in config.json only.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
