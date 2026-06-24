---
name: webapp
description: Launches the Photonic Cloud Lab web UI (FastAPI + React) — the browser-based dashboard for running experiments, viewing live results, and interacting with the AI scientist agent. Use this skill whenever the user wants to start the web app, open the web UI, launch the dashboard, see the browser lab, or serve the frontend — even casual phrasing like "open the web app", "start the server", "show me the dashboard", "launch the UI", "fire up the webapp", or "run the website". Use when this capability is needed.
metadata:
  author: jinming99
---

# Webapp Launcher

The user says `/webapp` (or anything that implies launching the web UI); you handle frontend build, server startup, browser open, and process management so they don't have to remember paths or flags.

## Why this skill exists

The `/pitch-demo --webapp` path bundles webapp launch inside the larger demo skill. This skill is the direct, lightweight alternative: just the web app, no TUI/Telegram/headless decision tree, no agent personality flags. It's the right tool when the user simply wants to work with the browser UI.

## Arguments

Parse `$ARGUMENTS`:

| Flag | Meaning | Default |
|---|---|---|
| `--dev` | Start the Vite dev server (`npm run dev` in `webapp/frontend/`) alongside the FastAPI backend. Enables hot-reload for frontend changes. | off |
| `--port <N>` | Override the FastAPI server port. | `8000` |
| `--no-open` | Don't auto-open the browser. | off |
| `--build` | Force-rebuild the frontend (`npm run build`) even if `webapp/static/index.html` exists. | off |
| `--stop` | Stop a running webapp server instead of starting one. | off |

## Workflow

### Step 1 — Stop mode (if `--stop`)

If the user passes `--stop`, find and kill the running webapp server:

```bash
bash .claude/skills/webapp/scripts/launch.sh stop ""
```

Report the result (stopped PID or "no server running") and exit — don't continue to preflight or launch.

### Step 2 — Pre-flight

Run the preflight script. It checks that the webapp dependencies are importable, the AI backend is available, the simulator works, and no conflicting server is already bound to the port.

```bash
python .claude/skills/webapp/scripts/preflight.py <port>
```

Where `<port>` is the parsed `--port` value (default `8000`).

On success, parse the JSON from stdout. On non-zero exit, relay the stderr message verbatim and stop.

Exit codes:
- `0` — ok
- `1` — port already in use (PID on stderr); ask the user whether to kill it or pick a different port
- `2` — no Claude backend (SDK or API key)
- `3` — SDK installed but `claude` CLI not on PATH
- `5` — webapp or simulator imports failed
- `6` — python-dotenv not installed
- `7` — node/npm not found (needed for frontend build)

### Step 3 — Frontend build

If `--build` is set, or `webapp/static/index.html` doesn't exist, the launch script builds the frontend automatically. If `--dev` is set, the build step is skipped (Vite serves directly).

### Step 4 — Launch

```bash
bash .claude/skills/webapp/scripts/launch.sh <mode> "<flags>"
```

Where `<mode>` is `serve` (default) or `dev` (if `--dev`), and `<flags>` includes `--port <N>` if overridden and `--no-open` if set.

#### Serve mode (default)

Starts the FastAPI server in the background. The server serves the built React app from `webapp/static/` and provides REST + WebSocket endpoints.

After launching, tell the user:

- The web lab is running at **http://localhost:<port>**
- Configure budget, personality, and starting hypothesis in the sidebar, then click **Start Experiment**
- Inject hypotheses or steering guidance via the command bar while the agent runs
- Tabs: **Live Lab** (experiment table + reasoning), **Charts** (FoM convergence, spectrum, crosstalk), **Notebook**, **Memory**
- Stop with: `/webapp --stop`

#### Dev mode (if `--dev`)

Starts both the FastAPI backend (port 8000) and the Vite dev server (port 5173) for frontend hot-reload. Useful when iterating on the React UI.

After launching, tell the user:

- Backend API at **http://localhost:<port>**
- Frontend dev server at **http://localhost:5173** (with hot-reload)
- Open **http://localhost:5173** in the browser for development
- Stop with: `/webapp --stop`

### Step 5 — Monitor

After launch, read the first few lines of the server log to confirm the server started cleanly. If there are errors, relay them immediately.

## Failure modes — fail loudly

Same project philosophy: don't silently fall back. If the port is occupied, don't pick another port — surface the conflict and ask. If the frontend build fails, show the npm error output.

## Bundled scripts

| Script | Responsibility |
|---|---|
| `scripts/preflight.py` | Environment checks: imports, AI backend, port availability. JSON on stdout, pointer on stderr, distinct exit codes. |
| `scripts/launch.sh` | `serve` starts FastAPI in background; `dev` starts FastAPI + Vite; `stop` kills running server. |

---
> Source: [jinming99/sous-chef](https://github.com/jinming99/sous-chef) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
