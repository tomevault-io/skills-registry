---
name: modern-cli-tools
description: Use at session start and before running shell commands involving Python, Node.js, file search, or HTTP requests. Use when this capability is needed.
metadata:
  author: atomikpanda
---

# Modern CLI Tools

## NEVER USE These Commands

These commands are **prohibited**. No exceptions, no fallback.

**Python:**
- `pip`, `pip3`, `pip install`, `pip freeze`
- `python -m venv`, `virtualenv`
- `python`, `python3` (to run scripts — use `uv run` instead)

**Node.js:**
- `npm`, `npm install`, `npm run`, `npx`
- `yarn`

**Node Versions:**
- `nvm`

**Shell:**
- `grep` — use `rg` (ripgrep)
- `find` — use `fd`

**HTTP:**
- `curl`, `wget` — use `xh`

## Modern Replacements

| Task | Command | Replaces |
|------|---------|----------|
| Install Python package | `uv add <pkg>` | pip install |
| Run Python tool (one-off) | `uvx <tool>` | pipx run / pip install + run |
| Run Python script | `uv run script.py` | python script.py |
| Run Python tests | `uv run pytest` | python -m pytest |
| Sync Python environment | `uv sync` | pip install -r requirements.txt |
| Manage Python version | `uv python install 3.12` | pyenv / system python |
| Install Node packages | `pnpm install` | npm install / yarn |
| Run Node script | `pnpm run <script>` | npm run / yarn |
| Install Node version | `fnm install <version>` | nvm install |
| Use Node version | `fnm use <version>` | nvm use |
| Search file contents | `rg <pattern>` | grep -r |
| Find files | `fd <pattern>` | find . -name |
| Make HTTP request | `xh GET <url>` | curl / wget |

## Tool Not Installed?

**Do NOT fall back to the legacy tool.** Stop and tell the user to install the modern tool:

| Tool | Official Site |
|------|--------------|
| uv / uvx | https://github.com/astral-sh/uv |
| pnpm | https://pnpm.io |
| fnm | https://github.com/Schniz/fnm |
| ripgrep (`rg`) | https://github.com/BurntSushi/ripgrep |
| fd | https://github.com/sharkdp/fd |
| xh | https://github.com/ducaale/xh |

Example message to user:
> "`rg` (ripgrep) is not installed. Install it from https://github.com/BurntSushi/ripgrep before continuing."

## Red Flags — You Are About to Violate This Skill

| Thought | Reality |
|---------|---------|
| "The tool isn't installed" | Tell the user to install it. Do NOT fall back to legacy tool. |
| "Just this once" | No exceptions. Use the modern tool or stop. |
| "This is a CI/container environment" | Still applies. Notify the user if the tool is unavailable. |
| "The existing script already uses pip/npm" | Tell the user to migrate. Do not propagate legacy tool usage. |
| "It's faster/easier to use grep here" | Use `rg`. It is faster. |
| "The user didn't ask me to use modern tools" | This skill is always active. It applies regardless. |

---
> Source: [atomikpanda/modern-cli-tools](https://github.com/atomikpanda/modern-cli-tools) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
