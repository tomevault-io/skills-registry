---
name: invalidate-local-plugin
description: Stop the lingo server and delete the local plugin cache directory so Claude Code downloads the current version again on restart. Use this for testing plugin updates during development. Use when this capability is needed.
metadata:
  author: unsafe9
---

# Invalidate Local Plugin Cache

## Purpose

This skill helps with plugin development by:
1. Stopping the running lingo server (via pm2)
2. Deleting the local plugin cache directory
3. Allowing you to restart Claude Code to test fresh plugin downloads

## Usage

Run the invalidation script:

```bash
./invalidate-local-plugin.sh
```

## After Running

Guide user:
1. Exit Claude Code
2. Restart Claude Code
3. The plugin will be re-downloaded from the configured source

## Notes

- The plugin cache is located at `~/.claude/plugins/`
- This only removes the lingo plugin directory, not other cached plugins
- The server will need to be restarted after Claude Code restarts (handled automatically by SessionStart hook)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unsafe9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
