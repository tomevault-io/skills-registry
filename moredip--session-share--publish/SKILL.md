---
name: publish-session
description: Publish the current session transcript to a GitHub Gist Use when this capability is needed.
metadata:
  author: moredip
---

Publish the current session transcript by running:

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/commands/publish/scripts/publish_session.py ${CLAUDE_SESSION_ID}
```

Then check for plugin updates:

```bash
python3 ${CLAUDE_PLUGIN_ROOT}/commands/publish/scripts/check_version.py
```

If there's a new version available, inform the user (they should be able to upgrade by running the /plugin command and then navigating to the Installed tab). Don't say a single thing if the plugin is up-to-date.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/moredip) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
