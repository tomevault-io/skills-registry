---
name: update-definitions
description: Refresh signature databases and definition files for every installed scanner — freshclam (ClamAV), rkhunter --update, lynis update info, AIDE differential check (does NOT promote a new baseline), debsecan suite refresh. Reads installed flags from plugin config and skips tools that aren't present. Triggers on "update AV definitions", "refresh signatures", "update clamav". Use when this capability is needed.
metadata:
  author: danielrosehill
---

# Update Definitions

Refresh signatures / databases for every installed scanner. Skips tools the config marks as not installed.

## Config

```
${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/linux-av-manager/config.json
```

Read `installed.*` flags. For each tool present, run its update path:

| Tool | Command | Notes |
|---|---|---|
| ClamAV | `sudo systemctl stop clamav-freshclam && sudo freshclam && sudo systemctl start clamav-freshclam` | Stop the service first to avoid lock contention. |
| rkhunter | `sudo rkhunter --update` | Updates property/signature data. **Do not** automatically run `--propupd` here — that re-baselines and would mask real changes. |
| Lynis | `sudo lynis update info` | Reports whether a newer Lynis is available; `apt upgrade lynis` if so. |
| chkrootkit | apt/dnf/pacman upgrade for the package | No separate signature feed. |
| AIDE | `sudo aide --check` and report deltas | This is a *check*, not an update. Surface deltas; only promote with explicit user instruction. |
| debsecan | `sudo apt update && debsecan --suite $(lsb_release -sc) --format detail` | Refresh apt index then list outstanding CVEs. |

## Output

Print a one-line per-tool status line:

```
clamav    ✓ updated  (main.cvd 6.x → 7.x; daily.cvd refreshed)
rkhunter  ✓ updated
lynis     ✓ no update available
aide      ⚠ 14 file deltas — review before promoting (sudo aide --update)
debsecan  ✓ 3 CVEs reported — see <scans_dir>/debsecan/<timestamp>.txt
```

Save AIDE/debsecan output (the only ones with real per-run findings) into `<scans_dir>/<tool>/<timestamp>.txt` so a later `scan` or audit can reference them.

## Notes

- Never auto-promote AIDE's new baseline.
- Never auto-run `rkhunter --propupd`.
- Do batch the refreshes — they're independent — but stream output as each finishes so the user sees progress.

---
> Source: [danielrosehill/Claude-Linux-AV-Manager-Plugin](https://github.com/danielrosehill/Claude-Linux-AV-Manager-Plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
