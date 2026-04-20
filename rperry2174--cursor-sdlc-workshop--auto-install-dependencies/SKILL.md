---
name: auto-install-dependencies
description: Automatically checks for and installs missing development tools (Homebrew, Node.js, npm, Git, GitHub CLI) before they're needed. Use when running commands that require these tools, when a command fails with "command not found", when setting up a project, or when scaffolding an MVP. Use when this capability is needed.
metadata:
  author: rperry2174
---

# Auto-Install Dependencies

## When to use

Activate whenever:
- About to run a command that depends on `brew`, `node`, `npm`, `npx`, `git`, or `gh`
- A command fails with "command not found" or a missing tool error
- Setting up a new project or scaffolding an MVP
- The user asks to install something or fix a broken setup

## Core behavior

**Always check before you run.** Before executing any command that depends on a tool, verify the tool is installed. If it's missing, install it — don't ask, just do it and explain what you did and why.

### Check-and-install order

Tools depend on each other. Install in this order:

1. **Homebrew** — the Mac package manager that installs everything else
2. **Git** — version control, needed for cloning repos and the workshop git flow
3. **GitHub CLI (`gh`)** — lets you create PRs, fork repos, and interact with GitHub from the terminal
4. **Node.js** (includes npm) — runs JavaScript outside the browser, needed for React projects

### How to check and install each tool

**Homebrew:**
```bash
# Check
command -v brew

# Install if missing
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Then add to PATH (Apple Silicon Macs)
eval "$(/opt/homebrew/bin/brew shellenv)"
```
Tell the user: "Homebrew is a package manager for Mac — think of it like an app store for developer tools. We need it to install everything else."

**Git:**
```bash
# Check
command -v git

# Install if missing
brew install git
```
Tell the user: "Git tracks changes to your code and lets your team collaborate without overwriting each other's work."

**GitHub CLI:**
```bash
# Check
command -v gh

# Install if missing
brew install gh
```
Tell the user: "The GitHub CLI lets you do GitHub things (fork repos, open pull requests) right from the terminal instead of clicking around the website."

**Node.js + npm:**
```bash
# Check
command -v node && command -v npm

# Install if missing
brew install node
```
Tell the user: "Node.js lets you run JavaScript outside the browser. npm is its package manager — it installs the libraries your React project needs."

## When a command fails

If any command fails with "command not found" or a similar error:

1. Identify which tool is missing from the error message
2. Install it using the steps above
3. Retry the original command
4. Explain what happened: "That command needs [tool], which wasn't installed yet. I just installed it — here's what it does: [one sentence]."

## Tone

These users are not developers. When installing something:
- **Name the tool** and say what it does in one plain sentence
- **Don't ask permission** — just install it and explain after
- **Don't dump version numbers or logs** — just confirm it worked
- If something goes wrong, explain the fix in simple terms

### Good example
> "I installed Node.js — it's what runs your React project. You're all set."

### Bad example
> "Node.js v20.11.0 has been installed via Homebrew to /opt/homebrew/bin/node. The npm registry is configured at https://registry.npmjs.org/. Your PATH has been updated."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rperry2174) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
