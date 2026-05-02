---
name: inspire
description: Use when the user asks to interact with the Inspire HPC platform - submitting GPU jobs, syncing code, managing notebooks, monitoring status, and executing commands on the Bridge runner. Also use when the user asks about inspire-cli commands, workflows, or configuration.
metadata:
  author: embodiedforge
---

# Inspire CLI

CLI for the Inspire HPC training platform. Run `inspire --help` and `inspire <command> --help` for flags and syntax — don't memorize them, they change often.

## What it can do

- **Setup**: `init --discover` (auto-detect projects/workspaces/compute groups via browser login)
- **Jobs**: `job create`, `job status/logs/list`, `job stop/wait`, `run` (quick submit with auto-sync)
- **Notebooks**: `notebook create/list/start/stop`, `notebook ssh` (tunnel + shell), `notebook top` (GPU monitoring)
- **Code sync**: `sync` (git push to shared filesystem), `bridge exec` (run remote commands)
- **File transfer**: `bridge scp` (upload/download via tunnel)
- **Tunnels**: `tunnel add/list/status`, `notebook ssh --save-as` (create reusable bridge profiles)
- **Info**: `resources list/nodes`, `project list`, `image list`, `config show/check`

## Platform knowledge (things you can't get from --help)

### Network topology
- **CPU / 4090 notebooks** → have internet. SSH auto-installs everything. Zero setup.
- **H100 / H200 notebooks** → NO internet. SSH needs pre-placed `rtunnel_bin` on shared filesystem, plus either `apt_mirror_url` or pre-placed `dropbear_deb_dir` for the SSH server.
- **Jobs** → NO internet. Same GPU clusters. All dependencies must be pre-installed in the image or on the shared filesystem.

### Shared filesystem
- `/inspire/hdd/global_user/<username>` — visible from ALL notebooks across ALL projects. Put SSH tools here.
- `/inspire/hdd/project/<project-slug>/<username>` — project-specific workdir. This is `target_dir` for code sync.
- These paths come from the API catalog (`init --discover`) but **must be verified via CPU notebook SSH** — the catalog can be wrong.

### Project auto-selection
- When no `--project` flag is given, auto-selection picks from available projects.
- Sort order: `project_order` (user preference) > `gpu_unlimited` (tiebreaker) > `priority` > name.
- `project_order` is set in `.inspire/config.toml` under `[defaults]` — list of project **names**.
- "Unlimited GPU hours" does NOT mean unlimited concurrent GPUs. A project can have no hour cap but still be full.

### SSH tunnel architecture
- `notebook ssh` opens a Jupyter terminal via WebSocket, runs a setup script (installs SSH server + rtunnel), then connects via the platform's HTTP proxy.
- First SSH to a fresh notebook takes 10-60s (setup). Subsequent connections reuse the running processes.
- `--save-as <name>` creates a reusable bridge profile. After that, `ssh <name>`, `bridge exec`, `sync`, `bridge scp` all work through it.
- Tunnels break when notebooks restart. `bridge exec/ssh` auto-reconnect for notebook-backed profiles.

## Rules the model should always follow

1. **Commit before sync** — `inspire sync` pushes the current git branch; uncommitted changes are lost.
2. **Jobs start in an unknown directory** — Always `cd $TARGET_DIR && ...` in job commands.
3. **Use `.` not `source`** for venv activation in job commands (POSIX compatibility).
4. **GPU nodes have NO internet** — pip install, git clone, curl all fail. Use a CPU notebook/bridge.
5. **Priority range is 1-10** — Outside this range causes API errors.
6. **Python output buffering** — `PYTHONUNBUFFERED=1` or `print(..., flush=True)` for live log tailing.
7. **Always run `--help`** before guessing flags — the CLI iterates fast and flags change.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/embodiedforge) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
