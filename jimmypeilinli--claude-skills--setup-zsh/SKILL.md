---
name: setup-zsh
description: Set up Zsh + Antigen + Oh-My-Zsh terminal environment on a new machine, including VS Code terminal configuration and bashrc migration. Use when the user asks to configure zsh, set up terminal, or initialize shell environment. Use when this capability is needed.
metadata:
  author: jimmypeilinli
---

# Setup Zsh Terminal Environment

You are setting up a complete Zsh terminal environment. Follow these steps in order. Report progress to the user after each step.

Target user: $ARGUMENTS (if empty, use the current user)

## Step 1: Check prerequisites

Run these checks in parallel:
- `which zsh` — check if zsh is installed
- `echo $SHELL` — check current default shell
- `uname -s && uname -m` — detect OS and architecture
- `ls ~/.antigen/antigen.zsh 2>/dev/null` — check if antigen exists
- `which eza 2>/dev/null` — check if eza exists

## Step 2: Install zsh (if not installed)

Detect the package manager and install:
- apt (Debian/Ubuntu): `sudo apt update && sudo apt install -y zsh`
- yum (CentOS/RHEL): `sudo yum install -y zsh`
- dnf (Fedora): `sudo dnf install -y zsh`
- pacman (Arch): `sudo pacman -S --noconfirm zsh`

## Step 3: Install Antigen (if not present)

```bash
mkdir -p ~/.antigen
curl -fsSL https://git.io/antigen -o ~/.antigen/antigen.zsh
```

## Step 4: Install eza (if not present)

Download the correct binary based on OS/arch:
- Linux x86_64: `eza_x86_64-unknown-linux-musl.tar.gz`
- Linux aarch64: `eza_aarch64-unknown-linux-gnu_no_libgit.tar.gz`

From: `https://github.com/eza-community/eza/releases/latest/download/`

Install to `~/.local/bin/eza` (create `~/.local/bin` if needed).

## Step 5: Migrate personalized config from .bashrc

**IMPORTANT**: Before writing `.zshrc`, read the user's existing `.bashrc` (or `.bash_profile`) and extract personalized content that should be migrated.

### What to extract

Scan `.bashrc` from the end backwards and look for these categories of personal config (ignore bash-specific boilerplate like `shopt`, `HISTCONTROL`, PS1 prompt, color support, bash completion, etc.):

- **Conda / Mamba init** — convert `shell.bash hook` to `shell.zsh hook`
- **Proxy settings** — `http_proxy`, `https_proxy`, `no_proxy`
- **NVM (Node.js)** — `NVM_DIR`, `nvm.sh` loader
- **CUDA settings** — `TORCH_CUDA_ARCH_LIST` etc.
- **API keys / tokens** — `ANTHROPIC_*`, `OPENAI_*`, etc.
- **Custom aliases** — any user-defined aliases
- **Source scripts** — `. "$HOME/.local/bin/env"` etc.
- **Other exports** — any custom `export` lines not covered above

### Ask for confirmation

Use `AskUserQuestion` to present the extracted items to the user, grouped by category. Let the user select (multiSelect) which ones to migrate. Each option should show the actual content that will be added.

### Categorization

When writing to `.zshrc`, group the migrated content by semantic category with clear section headers. For example:
```
# ----- Conda -----
# ----- Proxy -----
# ----- NVM (Node.js) -----
# ----- CUDA -----
# ----- Claude API -----
```

Do NOT lump unrelated items together (e.g. don't put API keys under "CUDA").

## Step 6: Write ~/.zshrc

**IMPORTANT**: Back up existing `.zshrc` first: `cp ~/.zshrc ~/.zshrc.bak.$(date +%Y%m%d%H%M%S)`

Write the `.zshrc` using the template in [zshrc.template](zshrc.template), then append the user-confirmed personalized config from Step 5 under the `# ----- User custom config -----` section at the end.

## Step 7: Set default shell to zsh

```bash
chsh -s $(which zsh)
```

If `chsh` fails (e.g. permission denied), inform the user to run:
```bash
sudo chsh -s $(which zsh) $(whoami)
```

## Step 8: Configure VS Code default terminal

Check and configure these VS Code settings files to set `"terminal.integrated.defaultProfile.linux": "zsh"`:

1. **Remote (vscode-server)**: `~/.vscode-server/data/Machine/settings.json`
2. **Local**: `~/.config/Code/User/settings.json`

For each file:
- If it exists: read it, check if `terminal.integrated.defaultProfile.linux` is already set. If not, add it (use the Edit tool to insert after the opening `{`).
- If it doesn't exist: create the directory and write a minimal settings.json with just this setting.
- If already configured: skip and report OK.

## Step 9: Final verification

Run these checks and report results:
- `zsh --version`
- `getent passwd $(whoami) | cut -d: -f7` — verify default shell
- `ls ~/.antigen/antigen.zsh` — verify antigen
- `~/.local/bin/eza --version 2>/dev/null` — verify eza
- Check VS Code settings files

## Summary

After completion, tell the user:
- Run `zsh` or re-login to start using the new shell
- In VS Code, reopen terminal (Ctrl+`) to use zsh
- List all installed plugins
- List all migrated personal config items

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jimmypeilinli) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
