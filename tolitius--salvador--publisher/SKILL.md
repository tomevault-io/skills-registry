---
name: publisher
description: deploys local artifacts (visuals, html) to configured remote destinations (e.g. ssh, s3). handles file transfer and permissions.
metadata:
  author: tolitius
---

# publisher

## routines

### publish
publishes a single visual to the configured provider.

**usage**:
```bash
python3 .claude/skills/publisher/scripts/publish.py <local_path> <slug>

```

**arguments**:

* `local_path`: relative path to the html file (e.g. `visuals/gravity.html`).
* `slug`: url-friendly identifier (e.g. `gravity-simulation`).

**requirements**:

* `resources/config.json` must exist.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tolitius) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
