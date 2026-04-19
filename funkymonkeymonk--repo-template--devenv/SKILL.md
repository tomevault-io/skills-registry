---
name: devenv
description: Manage development environments using devenv. Use for devenv.nix files, environment setup, dependency management. Use when this capability is needed.
metadata:
  author: funkymonkeymonk
---

# DevEnv Skill

## Core Operations
- Read/modify `devenv.nix` configuration
- Add packages to `packages = [ pkgs.package ];`
- Configure languages: `languages.python.enable = true;`
- Enable services: `services.postgres.enable = true;`
- Enter shell: `devenv shell`

## MCP Access
When skill loaded: `skill_mcp(mcp_name="devenv", tool_name="...", arguments='{...}')`
- Auto-connection with 5min idle timeout
- No manual setup required if `opencode-lazy-loader` plugin installed

## Key Files
- `devenv.nix` - Main configuration
- `devenv.yaml` - Optional YAML config
- `.envrc` - direnv configuration
- `treefmt.toml` - Formatter settings

## Common Commands
- `devenv shell` - Enter environment
- `devenv build` - Build environment
- `task format` - Format code
- `task --list` - Show tasks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/funkymonkeymonk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
