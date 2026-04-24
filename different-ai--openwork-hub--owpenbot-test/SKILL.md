---
name: owpenbot-test
description: | Use when this capability is needed.
metadata:
  author: different-ai
---

## Quick Usage (Already Configured)

### Run owpenbot + openwrk tests
```bash
skills/owpenbot-test/scripts/run-tests.sh
```

## First-Time Setup (If Not Configured)

1. Copy `skills/owpenbot-test/.env.example` to `skills/owpenbot-test/.env`.
2. Fill in:
   - `OWPENBOT_TEST_TOKEN_ENV`
   - `OWPENBOT_TEST_TOKEN_CLI`
   - (Optional) `OWPENBOT_DIR` and `OPENWORK_DIR` to override auto-detection.
3. Run the script above.

## Common Gotchas

- `.env` is optional but ignored by git; tokens should stay local.
- The script will auto-detect repos under `_repos/owpenbot` and `_repos/openwork` if dirs are not set.
- `.env.example` lists the minimum config required by the script.
- `OWPENBOT_TEST_TOKEN_ENV` is used by the CLI tests to seed Telegram config.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/different-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
