---
name: spice-install
description: Install Spice.ai OSS CLI and runtime. Use when asked to "install Spice", "set up Spice", or "get started with Spice". Use when this capability is needed.
metadata:
  author: neversight
---

# Spice.ai OSS Installation

Install the Spice CLI and runtime on macOS, Linux, Windows, or WSL.

## Quick Install

**macOS, Linux, and WSL:**
```bash
curl https://install.spiceai.org | /bin/bash
```

**Homebrew:**
```bash
brew install spiceai/spiceai/spice
```

**Windows (PowerShell):**
```powershell
iex ((New-Object System.Net.WebClient).DownloadString("https://install.spiceai.org/Install.ps1"))
```

## Verify Installation

```bash
spice version
```

If command not found, add to PATH:
```bash
export PATH="$PATH:$HOME/.spice/bin"
```

## Upgrade

```bash
spice upgrade
```

## Documentation

- [Installation Guide](https://spiceai.org/docs/installation)
- [Getting Started](https://spiceai.org/docs/getting-started)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
