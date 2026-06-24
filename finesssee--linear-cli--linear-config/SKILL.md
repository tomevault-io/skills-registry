---
name: linear-config
description: Configure linear-cli - auth (API key + OAuth), workspaces, diagnostics, setup wizard. Use when this capability is needed.
metadata:
  author: finesssee
---

# Configuration

```bash
# First-time setup wizard
linear-cli setup

# Set API key
linear-cli config set-key YOUR_API_KEY

# Show config
linear-cli config show

# Auth commands
linear-cli auth login                # Store API key
linear-cli auth oauth                # OAuth 2.0 browser flow (PKCE)
linear-cli auth oauth --client-id ID # Custom OAuth app
linear-cli auth status               # Check auth status (shows type, expiry)
linear-cli auth revoke               # Revoke OAuth tokens
linear-cli auth logout               # Remove key

# Workspaces
linear-cli config workspace-add work KEY
linear-cli config workspace-list
linear-cli config workspace-switch work
linear-cli config workspace-current

# Profiles
linear-cli --profile work i list     # Use profile

# Diagnostics
linear-cli doctor                    # Check config and connectivity
linear-cli doctor --fix              # Auto-fix common issues

# Shell completions (static)
linear-cli config completions bash > ~/.bash_completion.d/linear-cli

# Shell completions (dynamic, context-aware)
linear-cli completions dynamic bash >> ~/.bashrc
linear-cli completions dynamic zsh >> ~/.zshrc
linear-cli completions dynamic fish >> ~/.config/fish/completions/linear-cli.fish
```

## Environment Variables

| Variable | Purpose |
|----------|---------|
| `LINEAR_API_KEY` | API key override |
| `LINEAR_CLI_PROFILE` | Profile override |
| `LINEAR_CLI_OUTPUT` | Default output format |
| `LINEAR_CLI_YES` | Auto-confirm prompts |
| `LINEAR_CLI_NO_PAGER` | Disable pager |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/finesssee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
