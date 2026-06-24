---
name: nestos-bootstrap
description: Bootstrap the NestOS stack on a fresh Ubuntu 22.04 server where OpenClaw is already installed. Use when the user needs one-shot post-OS automation: silent XRDP desktop, 1Panel install, OpenResty + PHP runtime + bootstrap site, Chrome install, optional GitHub plugin pull, and qmd setup for OpenClaw memory. Use when this capability is needed.
metadata:
  author: Bigchx
---

# NestOS Bootstrap

Run the full bootstrap with visible progress and a final machine-readable summary.

## 6-Step SOP (optimized)

1. **Precheck + base packages**  
   Validate root, timezone, network; install required system packages.
2. **Desktop layer**  
   Install XRDP + XFCE non-interactively, set default desktop entry, install Chinese locale/fonts (fix garbled CJK in Chrome), and apply bundled wallpaper `scripts/BG.png` as XFCE default.
3. **Panel layer**  
   Install 1Panel, then initialize OpenResty app (use market default/latest available).
4. **Runtime + site layer**  
   Install PHP8 runtime (prefer 1Panel app, compose fallback), create bootstrap website, expose `http://<IPv4>:9000`.
5. **Client/tooling layer**  
   Install Chrome stable; copy bundled `chrome-extension/` into target host and also into `/root/Downloads/chrome-extension`; set developer mode preference; patch `/usr/share/applications/google-chrome.desktop` to include `--no-sandbox --no-first-run` for root desktop sessions; create `/root/Desktop/google-chrome-root.desktop`; create normal root launcher `/usr/local/bin/google-chrome-root` (preferred); keep preloaded launcher (`google-chrome-nestos`) as fallback; optionally pull extra plugin from GitHub URL.
6. **Memory layer + summary + reboot**  
   Install pinned qmd (`github:tobi/qmd`), run preload (`collection add + update + embed`), print full summary including live IPv4 and port reminders, create a cron watcher that only notifies after reboot is actually detected, then perform a host reboot automatically.

## Live Progress Rules (must)

- Keep terminal visible during long tasks.
- Emit heartbeat periodically (`[HEARTBEAT]`) with current step + elapsed time.
- Emit step markers (`[STEP]`) before each major stage.
- Never end silently: always print summary path + summary body.

### Channel-Aware Heartbeat Policy (strict)

When execution is expected to exceed 2 minutes, first detect available outbound channels from `openclaw status`.

1. If at least one messaging channel is enabled (Discord/Telegram/Feishu/WhatsApp):
   - Send start notice immediately: `Started | step=<step> | log=<path>`
   - Send milestone notice when each major step completes:
     `Step completed | step=<step> | next=<next_step>`
   - On success: send full summary + required verification results.
   - On failure: send error summary + last 50 log lines + retry command.

   **Mandatory active push implementation:**
   - After confirming bootstrap starts, create a temporary `cron` job that runs every **30s** and proactively reports current install progress to the **current conversation channel**.
   - Progress message format:
     `In progress | step=<step> | elapsed=<seconds>s | status=<running|blocked|retrying> | log=<path>`
   - Include dedupe/throttle logic in the cron task to avoid spam when there is no meaningful state change.
   - Immediately stop/remove this cron job when installation reaches terminal state (`success` or `failed`).
   - If cron creation fails, send fallback notice right away and switch to manual periodic updates.

   **CLI example (put this into runbook-level execution flow):**
   ```bash
   openclaw cron add \
     --name "nestos-install-progress" \
     --every "30s" \
     --session isolated \
     --message "Report current NestOS install progress to the current chat every run: step, elapsed seconds, and status. Stay concise and avoid duplicates when state is unchanged." \
     --announce
   ```

   **Tool-call equivalent (cron.add):**
   ```json
   {
     "action": "add",
     "job": {
       "name": "nestos-install-progress",
       "schedule": { "kind": "every", "everyMs": 30000 },
       "sessionTarget": "isolated",
       "payload": {
         "kind": "agentTurn",
         "message": "Report current NestOS install progress to the current chat every run: step, elapsed seconds, and status. Stay concise and avoid duplicates when state is unchanged."
       },
       "delivery": { "mode": "announce" }
     }
   }
   ```

2. If no messaging channel is enabled (TUI/Web only):
   - Send a one-time reminder at start:
     `No proactive push channel is configured; please send another message later to check progress.`
   - Keep writing progress to log file (`/var/log/NestOS-bootstrap.log`) and status file.
   - Send completion reminder note in final output text:
     `Installation is complete; please start a new message to fetch final results (this session cannot proactively push).`

3. For Web/TUI sessions where proactive push is unavailable:
   - Treat both as pull-based progress mode unless channel push is confirmed working.
   - Do not remain silent for >60s without either heartbeat push (channel mode) or explicit pull-mode reminder.

## Quick Start

```bash
bash scripts/bootstrap_NestOS.sh \
  --timezone "Asia/Shanghai" \
  --site-port 9000 \
  --plugin-url "<GITHUB_URL_OPTIONAL>"
```

## Outputs

- Runtime log: `/var/log/NestOS-bootstrap.log`
- Final summary: `/root/NestOS-bootstrap-summary.txt`
- Bundled extension source in skill package: `chrome-extension/`
- Installed extension copy for manual load: `/root/Downloads/chrome-extension`

Always return summary text to the user.

## Required Verification

```bash
systemctl is-active xrdp
systemctl is-active docker
docker ps --format '{{.Names}}\t{{.Status}}' | grep -Ei 'openresty|php|fpm' || true
curl -fsS http://127.0.0.1:9000 || true
command -v google-chrome
command -v qmd
```

## qmd Handling Policy (important)

- Install source must be pinned to avoid same-name package conflicts:

```bash
bun install -g github:tobi/qmd
```

- If qmd command set is incompatible, fail fast and reinstall pinned package.
- OpenClaw memory integration should prefer built-in `memory.backend=qmd` / `memory.qmd.*` route (not manual `mcporter.json` as default path).
- If platform-side `memory_search` is unstable/timeouts, use direct qmd (`qmd search / vsearch / query`) as operational fallback.

## Security + Network Reminder

Summary must include reminder to open these ports when needed:
- `3389` (XRDP)
- `1Panel actual port` (use the value from `1pctl user-info` / `1panel user-info`, do not hardcode an example)
- `80/443` (Web)
- `${SITE_PORT}` (bootstrap site)

Also replace `$LOCAL_IP` placeholders with detected host IPv4 in final output.

## Root Desktop Chrome Launch Fix (must)

On root XRDP desktops, system Chrome icon can fail because default `Exec=` omits root-safe flags. Bootstrap must enforce this after Chrome install:

- Patch `/usr/share/applications/google-chrome.desktop` `Exec=` lines to include `--no-sandbox --no-first-run`
- Create root desktop shortcut: `/root/Desktop/google-chrome-root.desktop`

Expected `Exec=` pattern:

```
/usr/bin/google-chrome-stable --no-sandbox --no-first-run ...
```

Preferred runtime launch command (normal mode):

```bash
DISPLAY=:10 /usr/bin/google-chrome-stable --no-sandbox --no-first-run --new-window "<URL>"
```

Fallback only when extension force-load is required:

```bash
DISPLAY=:10 /usr/local/bin/google-chrome-nestos --no-sandbox --no-first-run --new-window "<URL>"
```

Include this in final summary so operators know why root icon works.

## Chrome Relay Guidance (must show to user)

After Chrome is installed, provide this operator hint in the run output:

- This skill can launch system Chrome directly; after opening Chrome, proceed to browser relay takeover.
- **Chrome cannot be controlled by tools before tab attach/relay is enabled.**
- If the operator only needs to open a web page first, run this in the XRDP session:

```bash
DISPLAY=:10 /usr/bin/google-chrome-stable --no-sandbox --no-first-run --new-window "https://www.google.com"
```

Then instruct the operator to attach/relay the tab before expecting browser tool control.

## Memory Writeback Convention (must)

After finishing a NestOS bootstrap task, explicitly write key durable decisions into long memory (`MEMORY.md`) and the current daily note (`memory/YYYY-MM-DD.md`) when running in main session context.

> **IMPORTANT FOR ALL COLLABORATORS / OTHER AGENTS:**
> If you read this SKILL and apply/update any operational rule (especially browser opening behavior), you must also write the same durable rule into `MEMORY.md`.
> Do not keep the change only in this SKILL file.

Minimum required memory items:
- Skill/package path updates (e.g., `skills/nestos-bootstrap`, `dist/nestos-bootstrap.skill`)
- Chrome relay guidance contract and XRDP launch command
- Any newly added mandatory post-install steps (e.g., service restarts)
- Any changed progress-reporting policy that affects operator expectations
- **Browser opening policy updates** (must be mirrored into `MEMORY.md`)

When responding to collaborators, explicitly remind that these conventions are part of this SKILL and must be persisted in long-term memory (`MEMORY.md`) as well.

## Browser-First Web Access Reminder (must)

When web lookup or site access is needed during/after bootstrap:

- **Always use the system visual Chrome window first** (XRDP/X11 GUI), not `curl`/raw fetch as the primary path.
- **Do not default to page grabbing/scraping checks** when the operator asks to “open a website” or “看看页面”.
- Launch Chrome with the XRDP-safe visual command below, then perform browsing in that window:

```bash
DISPLAY=:10 /usr/bin/google-chrome-stable --no-sandbox --no-first-run --new-window "https://www.bigchx.com"
```

- If a target page is requested, replace the URL directly (for example `https://x.com/elonmusk`).
- Use fetch/curl only as a secondary diagnostic fallback when GUI cannot be used, and state clearly that it is fallback-only.
- In operator-facing wording, clearly remind: “Chrome is installed and ready; we’ll open it in the system visual desktop session.”

## Notes

- Keep secrets local; do not commit generated credentials.
- This skill is for bootstrap/install flow; hardening/audit belongs to a separate security workflow.

---
> Source: [Bigchx/NestOS](https://github.com/Bigchx/NestOS) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
