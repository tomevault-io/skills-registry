---
name: bootstrap-project
description: Bootstrap an empty directory into a runnable project from the AgentSystemLabs/launch-kit template — clones the template, strips its git history, runs setup (env file, install, docker compose), launches the dev server, and diagnoses missing prerequisites (Node, Docker, Docker Compose, git, package manager) with OS-specific install hints. Use when the user says "init launch-kit", "/bootstrap-project", "start a new launch-kit project", "scaffold launch-kit here", "set up launch-kit", "new project from launch-kit", or runs Claude in an empty directory and asks to spin up the AgentSystemLabs starter. Skip for: existing projects with code already present (refuse or require explicit override), templates other than AgentSystemLabs/launch-kit, or pure prereq install (use the OS package manager directly). Use when this capability is needed.
metadata:
  author: webdevcody
---

> **User-question protocol:** Whenever this skill needs the user to pick between options, confirm an action, or answer a multiple-choice prompt, you MUST call the `AskUserQuestion` tool to render a proper interactive picker. Do NOT print numbered options as plain text and wait for the user to type a number — that produces a degraded UX. Free-form questions (open-ended typing) may be asked in prose, but any time you would write "1) … 2) … 3) …", use `AskUserQuestion` instead.


# bootstrap-project

Bootstrap an empty directory into a running launch-kit app. The phases below are ordered — do not skip ahead. If a phase fails, fix the cause before continuing.

**Template repo:** `https://github.com/AgentSystemLabs/launch-kit`

---

## Phase 1 — Verify the target directory is empty

Run `ls -A` in CWD. Acceptable contents: nothing, or only `.claude/`, `.DS_Store`.

If `.git` is present, check for commits before doing anything else:

```bash
git -C . log --oneline -1 2>/dev/null
```

- **No output** → empty `.git` (e.g. just-run `git init`); safe to `rm -rf .git` in Phase 3.
- **Commit shown** → this is the user's existing repo. **Stop.** Do not `rm -rf .git`. Ask whether to (a) pick a subdirectory, (b) abort, (c) clone into the existing repo as a subtree (advanced — confirm explicitly).

If any other files are present:
- Stop. Ask: "This directory contains [list]. Cloning would overlay these files. Options: (a) pick a subdirectory, (b) abort, (c) proceed anyway (risk of overwrite)."
- Do not proceed without an explicit answer. Default is abort.

---

## Phase 2 — Prereq check

Run all checks **in parallel** in a single Bash call. Report a table of results before installing anything.

```bash
{ git --version; node --version; docker --version; docker compose version; } 2>&1
```

Also detect the package manager the template wants. Strategy: clone first (Phase 3), then read `package.json` `packageManager` field and any lockfile (`pnpm-lock.yaml`, `bun.lock`, `package-lock.json`, `yarn.lock`). Do **not** guess before cloning.

For each missing tool, surface a fix block matching the user's OS (`uname -s`):

| Tool | macOS (Homebrew) | Linux (apt) | Windows |
|---|---|---|---|
| git | `brew install git` | `sudo apt install git` | `winget install Git.Git` |
| node | `brew install node` (or use `nvm`/`fnm`/`mise`) | `curl -fsSL https://fnm.vercel.app/install \| bash` | `winget install OpenJS.NodeJS.LTS` |
| docker | Install Docker Desktop | `curl -fsSL https://get.docker.com \| sh` | Install Docker Desktop |
| docker compose | bundled with Desktop | `sudo apt install docker-compose-plugin` | bundled with Desktop |

**Critical distinctions:**
- `docker --version` succeeds but `docker info` fails → Docker is installed but daemon is not running. Fix: start Docker Desktop / `sudo systemctl start docker`. Do **not** suggest reinstall.
- `docker compose version` (v2, space) vs `docker-compose --version` (v1, hyphen) — prefer v2. If only v1 exists, warn and suggest upgrading; do not silently substitute.
- Check Node major version against the template's `engines.node` (read after clone). If too old, suggest `nvm`/`fnm`/`mise` rather than a system reinstall.

Do not auto-install prereqs. Print the commands and let the user run them. After they confirm, re-run the checks.

---

## Phase 3 — Clone the template

Run each line as a **separate** Bash call — Claude Code's safety check interrupts on `&&`/`;` chains:

```bash
git clone --depth=1 https://github.com/AgentSystemLabs/launch-kit.git .
```
```bash
rm -rf .git
```
```bash
git init -q
```
```bash
git add -A
```
```bash
git commit -q -m "Initial commit from launch-kit"
```

If `git clone` fails:
- HTTPS auth prompt → repo is public; this means a network/proxy issue, not auth. Tell the user.
- `destination path '.' already exists and is not an empty directory` → return to Phase 1.

---

## Phase 4 — Read the template's actual setup contract

Do not assume. Read these files in parallel and let them drive the next phase:

- `README.md` — look for "Getting Started" / "Quickstart"
- `package.json` — `packageManager`, `engines`, `scripts.dev`, `scripts.setup` (if any)
- `.env.example` (or `.env.template`) — required env vars
- `docker-compose.yml` / `compose.yaml` — services that must be up before dev
- `Makefile` / `justfile` / `mise.toml` — task runners

Build a setup checklist from what you actually find. If the README has explicit setup steps, those win over the inferred steps below.

---

## Phase 5 — Setup

Execute in order. Stop on first failure and diagnose.

1. **Env file:** if `.env.example` exists and `.env` does not → `cp .env.example .env`. Then read `.env` and flag any values that are placeholders the user must fill (e.g. `CHANGE_ME`, empty secrets, API keys). List them; do not invent values.
2. **Install deps:** use the package manager from `packageManager` field, else from lockfile. Run install with the matching command (`pnpm install`, `bun install`, `npm install`, `yarn install`).
3. **Compose services:** if a compose file exists → `docker compose up -d`. Then `docker compose ps` to confirm services are healthy. If a service exits, dump its logs: `docker compose logs <svc> --tail=50`.
4. **DB migrate / seed:** decide which script to run in this order:
   1. The exact command the README's setup section names → run it.
   2. Else if exactly one DB script exists (`db:push`, `db:migrate`, `migrate`, `prisma migrate dev`, `drizzle-kit push`, etc.) → run it.
   3. Else (multiple candidates, none documented) → list them and ask which to run. Never run all of them.

---

## Phase 6 — Launch

Run the dev script (usually `dev`) in the **background** so the user keeps control of the terminal. Capture the URL it prints (look for `localhost:` lines) and report it.

After ~5 seconds, fetch the root URL once to confirm it responds. If it doesn't:
- Port already in use → identify with `lsof -i :<port>` and ask the user how to resolve (kill it, or change the port via env var).
- Module-not-found → install step likely failed silently; re-run install.
- Compose service connection refused → re-check Phase 5 step 3.

Hand off with: the local URL, the background task ID for the dev server, and any placeholder env values the user still needs to fill.

---

## Debugging missing pieces

When something breaks, work the contract — don't guess:

- **"command not found"** → Phase 2 prereq is missing, even if it was "installed" earlier in the session (PATH not refreshed). Have the user open a new shell.
- **`EACCES` on install** → never suggest `sudo npm install`. The user's Node setup is wrong; suggest `nvm`/`fnm`/`mise`.
- **`Cannot connect to the Docker daemon`** → daemon not running. Not a reinstall.
- **`docker compose` unknown command** → v1 only; user needs the compose plugin.
- **Installed Node major < `engines.node`** → do not reinstall Node system-wide. Suggest `nvm use <ver>` / `fnm use <ver>` / `mise use node@<ver>` (or `nvm install <ver>` if not yet installed). Re-run from Phase 5 step 2 in the new shell.
- **Env var referenced in code but missing from `.env.example`** → real bug in the template; report the file:line so the user can open an issue upstream.

---

## NEVER

- **NEVER run `git clone` into a non-empty directory without Phase 1 confirmation**
  **Instead:** verify emptiness, then clone.
  **Why:** The user may have unrelated work in CWD; cloning silently merges files and `git init` would discard their history.

- **NEVER guess the package manager**
  **Instead:** read `packageManager` in `package.json` and the lockfile after Phase 3.
  **Why:** Running `npm install` on a pnpm/bun project produces a wrong lockfile and subtly broken `node_modules`.

- **NEVER auto-install Docker, Node, or other prereqs**
  **Instead:** print the OS-specific command and wait for the user to run it.
  **Why:** System-level installs need sudo, change the user's global toolchain, and may conflict with existing version managers (nvm, asdf, mise).

- **NEVER substitute `docker-compose` (v1) for `docker compose` (v2) silently**
  **Instead:** detect the gap, warn, and suggest installing the v2 plugin.
  **Why:** v1 is EOL and has subtly different compose-file semantics; silent substitution produces confusing runtime errors.

- **NEVER commit `.env` after copying from `.env.example`**
  **Instead:** confirm `.env` is in `.gitignore` before the initial commit; if it isn't, add it first.
  **Why:** placeholder secrets become real later, and once committed they leak through git history even if the file is later removed.

- **NEVER invent values for placeholder env vars**
  **Instead:** list them and ask the user.
  **Why:** Made-up API keys/secrets fail at runtime with cryptic errors and can leak into `.env` commits.

- **NEVER run the dev server in the foreground**
  **Instead:** launch it in the background and report the URL + task ID.
  **Why:** A foreground process blocks the conversation; the user can't ask follow-up questions or have you debug.

---
> Source: [webdevcody/go-mailing-list](https://github.com/webdevcody/go-mailing-list) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
