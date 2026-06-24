---
name: lsp-doctor
description: Automated LSP diagnostic and repair tool for TypeScript, Vue (Volar), and C# (csharp-ls) servers. Use when language server features (autocomplete, diagnostics, definitions) fail, type checking is broken, or servers crash during fresh environment setup. This skill provides a cross-platform (macOS, Linux, Windows) workflow to identify path misconfigurations, missing runtimes (e.g., .NET 8 for csharp-ls), and binary accessibility issues across different AI agent processes. Use when this capability is needed.
metadata:
  author: yusyuan9224
---

# lsp-doctor

Comprehensive diagnostic and repair workflow for Language Server Protocol (LSP) issues in development environments. This skill ensures your AI agent process can correctly locate, execute, and communicate with TypeScript, Vue, and C# language servers across macOS, Linux, and Windows.

## Overview

LSP failures often stem from environment mismatch where the agent process lacks the user's login shell PATH or required runtimes. lsp-doctor automates the detection and remediation of these issues by auditing the environment and applying persistent fixes.

## When to Use

- **Diagnostic Failures**: Your `lsp_diagnostics` tool returns empty results or errors on valid code.
- **Server Crashes**: The language server fails to start or times out repeatedly.
- **Missing Features**: "Go to definition" or autocompletion stops working.
- **Fresh Setup**: After installing new SDKs (.NET, Node.js) or switching AI agents.

## Diagnostic Workflow

Follow these steps to restore LSP functionality:

1.  **Verify Initial State**: Run `lsp_diagnostics` (or your agent's equivalent tool) on a representative source file (e.g., `.ts`, `.vue`, or `.cs`). If it fails or returns unexpected errors, proceed.
2.  **Identify Platform**: Determine if the current environment is Unix-based (macOS/Linux) or Windows.
3.  **Run Diagnostics**: Execute the diagnostic script with the `--diagnose-only` flag to identify root causes without making changes.
4.  **Apply Fixes**: If issues are found, run the script with `--fix-all`.
5.  **Verify & Restart**: Confirm the fix by re-running diagnostics. Note: C# LSP changes usually require an agent restart to take effect.

## Common Root Causes

- **Binary not in PATH**: The agent process often starts with a minimal PATH that does not include user-installed binaries (npm global, dotnet tools).
- **Missing .NET Runtime**: `csharp-ls` specifically requires a .NET 8 runtime. Even if the .NET 9 SDK is installed, the server may fail if the .NET 8 runtime is missing or not located via `DOTNET_ROOT`.
- **MSBuild not found**: C# projects require MSBuild to be discoverable. This often fails if environment variables like `DOTNET_ROOT` or `MSBUILD_EXE_PATH` are missing from the agent's environment.
- **Non-Persistent Shell PATH**: Manually exporting PATH in a session often fails to persist across agent restarts or new terminal instances.

## Script Reference

The skill includes two primary scripts that handle environment auditing and symlink/wrapper creation for all detected AI agent binary directories.

### macOS & Linux (`scripts/fix-lsp.sh`)

Usage: `bash scripts/fix-lsp.sh [flags]`

- `--diagnose-only`: Scan the environment and report issues without modifying files.
- `--fix-all`: Apply all recommended fixes, including symlinking binaries and setting environment variables.
- `--server [name]`: Target a specific server: `typescript`, `vue`, or `csharp`.

### Windows (`scripts/fix-lsp.ps1`)

Usage: `powershell -ExecutionPolicy Bypass -File scripts/fix-lsp.ps1 [params]`

- `-DiagnoseOnly`: Perform a read-only scan.
- `-FixAll`: Apply all registry and path fixes.
- `-Server [name]`: Target a specific server.

## Agent-Agnostic Fix Strategy

The scripts detect existing AI agent binary directories (e.g., `~/.opencode/bin/`, `~/.claude/bin/`, `~/.cursor/bin/`) and ensure the required LSP binaries are symlinked or wrapped within **all** found locations. If no agent-specific directory is identified, the scripts attempt to update the system or user PATH to ensure global accessibility.

## Platform Notes

### macOS & Linux
- Ensure `node` and `npm` are available.
- For C#, the script may prompt to install `csharp-ls` via `dotnet tool install`.

### Windows
- Execution policy must be bypassed to run the `.ps1` script.
- The script handles Windows-specific PATH character limits and Registry-based environment variables.

## Resources

- `scripts/fix-lsp.sh`: Main diagnostic and repair script for Unix-like systems.
- `scripts/fix-lsp.ps1`: Main diagnostic and repair script for Windows systems.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yusyuan9224) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
