---
name: install-advanced
description: Install the optional advanced security-audit layer on top of the core AV set — Lynis (system hardening audit), chkrootkit (second-opinion rootkit scanner), AIDE (file integrity DB), debsecan (Debian/Ubuntu CVE scanner). Lets the user pick a subset; doesn't force the full stack. Triggers on "install lynis", "add advanced AV tools", "install rootkit/audit tools". Use when this capability is needed.
metadata:
  author: danielrosehill
---

# Install Advanced

Layered on top of `install-core`. The user picks which of these they want — none are mandatory and they have different audiences (Lynis is broad hardening; AIDE is intrusion-detection baseline; chkrootkit complements rkhunter; debsecan is Debian-family CVE scanning).

## Config

```
${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/linux-av-manager/config.json
```

If missing → call `onboard` first. Recommend (but don't require) running `install-core` first.

## Packages

| Tool | Purpose | Debian/Ubuntu (apt) | Fedora (dnf) | Arch (pacman) |
|---|---|---|---|---|
| Lynis | System hardening audit | `lynis` | `lynis` | `lynis` |
| chkrootkit | Second-opinion rootkit scanner | `chkrootkit` | `chkrootkit` | `chkrootkit` (AUR) |
| AIDE | File integrity baseline | `aide` | `aide` | `aide` |
| debsecan | Debian/Ubuntu CVE scanner | `debsecan` | n/a | n/a |

Skip debsecan automatically on non-Debian systems; flag the omission.

## Steps

1. **Ask which to install.** Present the table; accept "all" or a subset. Default: none, let the user pick.
2. **Install** in one batched command per package manager.
3. **Per-tool post-install:**
   - **Lynis** — no setup; ready to run. Suggest `sudo lynis audit system` as the canonical command. (`scan` skill wraps this.)
   - **chkrootkit** — no setup; ready. Note that some "INFECTED" results from chkrootkit are well-known false positives (e.g. on `bindshell`); the `scan` skill flags these.
   - **AIDE** — initialise the baseline: `sudo aideinit` (Debian wrapper) or `sudo aide --init` then move `/var/lib/aide/aide.db.new` → `/var/lib/aide/aide.db`. **Only run on a known-clean system** — surface this and let the user defer.
   - **debsecan** — no setup; ready. Note it works against the configured suite (`/etc/debsecan/config`).
4. **Update `config.json`** — set the relevant `installed.<tool>` flags to true.
5. **Suggest next steps** — `update-definitions` to sync everything, or `scan` to run.

## Notes

- AIDE's database lives at `/var/lib/aide/aide.db`. After legitimate system changes (package updates, config edits), the user must rebuild via `sudo aide --update` and promote the new DB. Mention this — false positives are otherwise constant.

---
> Source: [danielrosehill/Claude-Linux-AV-Manager-Plugin](https://github.com/danielrosehill/Claude-Linux-AV-Manager-Plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
