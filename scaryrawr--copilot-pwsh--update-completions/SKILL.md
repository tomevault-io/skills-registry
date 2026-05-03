---
name: update-completions
description: > Use when this capability is needed.
metadata:
  author: scaryrawr
---

# Update Copilot CLI PowerShell Completions

## When to Use

- When the Copilot CLI has been updated and completions may be out of date
- When asked to update, sync, or refresh the PowerShell completions
- When new flags, subcommands, or help topics have been added to the CLI

## Instructions

1. Run the following commands to gather the current CLI help output:
   ```powershell
   copilot --help
   copilot help commands
   copilot help config
   copilot help environment
   copilot help logging
   copilot help permissions
   copilot init --help
   copilot update --help
   copilot version --help
   copilot login --help
   copilot plugin --help
   copilot plugin install --help
   copilot plugin uninstall --help
   copilot plugin update --help
   copilot plugin list --help
   copilot plugin marketplace --help
   copilot plugin marketplace add --help
   copilot plugin marketplace remove --help
   copilot plugin marketplace list --help
   copilot plugin marketplace browse --help
   ```

2. Compare the help output against the existing completions in `copilot.pwsh.psm1`.

3. Update `copilot.pwsh.psm1` to reflect any changes:
   - Add entries to the relevant `$script:_*` hashtables/arrays in the **Completion Data** region
   - Remove entries for deprecated/removed flags or subcommands
   - Update descriptions to match current help text
   - Add new subcommands or help topics
   - If a new flag accepts a value, add it to `$script:_flagsWithValues`
   - If a new flag has enumerated values, add a handler in the `$prevToken` check section of the completer

4. Update the fallback model list in `$script:_models` if the set of available models has changed. Parse the model list from `copilot --help` output under the `--model` section.

5. Validate changes by importing the module and triggering completions:
   ```powershell
   Import-Module .\copilot.pwsh.psm1 -Force
   # Verify no parse errors
   $null = [System.Management.Automation.Language.Parser]::ParseFile(
       (Resolve-Path .\copilot.pwsh.psm1).Path, [ref]$null, [ref]$null)
   ```

## Key Files

- `copilot.pwsh.psm1` — All completion logic; organized into three regions:
  - **Completion Data** — Static `$script:_*` hashtables and arrays
  - **Dynamic Completion Helpers** — Functions that shell out to `copilot` at runtime with 30s TTL caching
  - **Completer Registration** — `Register-ArgumentCompleter -Native -CommandName copilot` block
- `copilot.pwsh.psd1` — Module manifest (exports nothing; side effect is completer registration)

## Conventions

- All module-level state uses `$script:` scope prefix (e.g., `$script:_topLevelCommands`)
- Completion data variables are prefixed with `_` (e.g., `$script:_globalFlags`, `$script:_models`)
- Flag dictionaries use `[ordered]@{}` to preserve display order in completions
- Dynamic completions parse bullet-point output (`•`) from `copilot` CLI using regex
- `[Console]::OutputEncoding` is temporarily set to UTF-8 and restored in `finally` blocks when invoking external commands
- The manifest exports nothing (`FunctionsToExport = @()`); the module's only side effect is argument completer registration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scaryrawr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
