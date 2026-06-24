---
name: setup
description: First-run setup for the obs-mgmt plugin. Verifies OBS Studio is installed, enables and configures obs-websocket (bundled in OBS 28+), stores connection details in $CLAUDE_USER_DATA, and registers royshil/obs-mcp as an MCP server in Claude Code so later skills can drive OBS programmatically. Run this once before any other skill in this plugin. Use when this capability is needed.
metadata:
  author: danielrosehill
---

# OBS Plugin Setup

This skill is the prerequisite for every other skill in `obs-mgmt`. After it succeeds, later skills assume:

- A reachable OBS instance with obs-websocket enabled.
- An `obs-mcp` MCP server registered in Claude Code.
- Connection details persisted under `$CLAUDE_USER_DATA/obs-mgmt/config.json`.

## Data locations — two env vars to set

This plugin separates **plugin config** (small pointers, owned by the plugin) from **user-owned data** (backups, exported scene collections, notes — owned by the user). Two env vars control where each lives:

| Env var | Purpose | Default if unset |
|---|---|---|
| `OBS_CONFIG_PATH` | Override the auto-detected OBS config directory. Useful for non-standard installs, custom `--profile` flags, or running multiple OBS configs side by side. | Resolved by `detect-install` skill from install type. |
| `OBS_WORKSPACE` (the **management workspace**) | User-owned folder for config backups, exported scene JSON, recording notes, and any docs Claude generates about the user's OBS setup. | Prompt the user during setup; suggest `~/Documents/OBS-Workspace/` or `~/repos/obs-workspace/`. |

Setup will:

1. Resolve the plugin data root for **config pointers only**:

   ```bash
   DATA_ROOT="${CLAUDE_USER_DATA:-${XDG_DATA_HOME:-$HOME/.local/share}/claude-plugins}/obs-mgmt"
   mkdir -p "$DATA_ROOT"
   ```

2. Bootstrap the **management workspace** by delegating to the `init-workspace` skill. That skill prompts for the path, scaffolds the layout (`backups/`, `scenes/`, `plugins/`, `docs/`), and offers to turn it into a private GitHub repo so the workspace is versioned. Persist the export in `~/.bashrc` / `~/.zshrc` so it's stable across sessions:

   ```bash
   export OBS_WORKSPACE="$HOME/Documents/OBS-Workspace"
   ```

3. Persist pointers in `$DATA_ROOT/config.json`:

   ```json
   {
     "install_type": "native|flatpak|snap|appimage",
     "config_path": "/home/<user>/.config/obs-studio",
     "workspace_path": "/home/<user>/Documents/OBS-Workspace",
     "websocket": { "host": "localhost", "port": 4455 },
     "mcp_bundled": true,
     "setup_complete": true
   }
   ```

   Never store the websocket password in `config.json` — it stays in the user's shell env (`OBS_WEBSOCKET_PASSWORD`).

4. The workspace layout is scaffolded by `init-workspace` — see that skill for details. User-owned content (backups, exports, notes) **must** live under the management workspace, never under `$CLAUDE_USER_DATA`. Claude can freely write docs/notes/exports into `$OBS_WORKSPACE/docs/` to build up a knowledge base about the user's specific OBS rig; if the workspace is a git repo, those writes can be committed for version history.

## Step 1: Detect / verify OBS install

Delegate to the `detect-install` skill in this plugin. It returns one of `native`, `flatpak`, `snap`, or `missing`. If `missing`, stop and tell the user how to install OBS for their distro; do not proceed.

If the user has set `OBS_CONFIG_PATH` in their env, prefer that over the auto-detected path and validate it exists and contains a `global.ini` (the OBS marker file).

## Step 2: Enable obs-websocket

OBS 28+ bundles obs-websocket v5. The user must enable it once via the GUI (it cannot be enabled headlessly without editing config files OBS may overwrite):

1. Open OBS → **Tools → WebSocket Server Settings**.
2. Tick **Enable WebSocket server**.
3. Leave port at `4455` unless it conflicts.
4. Tick **Enable Authentication**, click **Show Connect Info**, and copy the password.
5. Click **Apply** then **OK**.

Prompt the user for the password and store it in `config.json` (chmod 600 the file). Do not echo the password back.

For older OBS (<28), instruct the user to install the standalone `obs-websocket` plugin from https://github.com/obsproject/obs-websocket/releases and restart OBS before continuing.

## Step 3: obs-mcp is bundled with this plugin

This plugin ships [royshil/obs-mcp](https://github.com/royshil/obs-mcp) as a bundled MCP server via `.mcp.json` at the plugin root — no manual `claude mcp add` needed. When the plugin is enabled, Claude Code spawns the server automatically via `npx -y obs-mcp@latest`.

The bundled server reads two environment variables:

- `OBS_WEBSOCKET_URL` (default `ws://localhost:4455`)
- `OBS_WEBSOCKET_PASSWORD` (required)

Set them in the user's shell profile so they're inherited by Claude Code:

```bash
# ~/.bashrc or ~/.zshrc
export OBS_WEBSOCKET_PASSWORD="<password from step 2>"
# Optional, only if non-default:
# export OBS_WEBSOCKET_URL="ws://<host>:<port>"
```

Reload the shell, then restart Claude Code so the MCP server picks up the new env. Verify with:

```bash
claude mcp list
```

The `obs` server should appear and report `connected`.

Prerequisite: Node.js 18+ on PATH so `npx` can fetch `obs-mcp`. If the user prefers a pinned local install, they can `npm i -g obs-mcp` and Claude Code's npx call still resolves it.

## Step 4: Validate

In the same Claude Code session (or after a restart so the MCP server loads), call an `obs-mcp` tool that lists scenes — e.g. `GetSceneList` or equivalent. A successful response confirms:

- obs-websocket is reachable.
- The password is correct.
- obs-mcp is registered and running.

If the call fails:

- **Connection refused** → OBS isn't running, or websocket isn't enabled. Re-run step 2.
- **Auth failed** → password mismatch. Re-prompt and rewrite `config.json`.
- **Tool not found** → obs-mcp didn't register. Check `claude mcp list` and re-run step 3.

## Step 5: Mark setup complete

Write `config.json` with `"setup_complete": true` and a timestamp. Other skills in this plugin should read this file and refuse to run if setup is incomplete, pointing the user back here.

---
> Source: [danielrosehill/Claude-OBS-Plugin](https://github.com/danielrosehill/Claude-OBS-Plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
