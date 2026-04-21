---
name: dependency-installer
description: Install packages at runtime inside the Docker container. Use uv for Python, npm for Node.js, apt-get for system packages. Use when this capability is needed.
metadata:
  author: iliagerman
---

# Dependency Installer

Runtime package installer for the Docker container. Installs dependencies that skills need.

## Available Package Managers

- **uv**: Python packages (`uv pip install <package>`)
- **npm**: Node.js packages (`npm install -g <package>`)
- **apt-get**: System packages (`apt-get install -y <package>`)
- **cargo**: Rust CLI tools (`cargo install <crate>`)
- **brew**: macOS Homebrew packages (`brew install <package>`)
- **url**: Download a binary to `/usr/local/bin` (`curl -fsSL -o /usr/local/bin/<name> <url> && chmod +x ...`)

## Usage

The agent calls the `install_package` tool to install dependencies at runtime.

## Examples

```
Install the requests Python library
Install axios npm package
Install ffmpeg system package
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iliagerman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
