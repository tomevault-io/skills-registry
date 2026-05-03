---
name: windows-folder-linker
description: Create Windows symbolic links (symlinks) to make one folder point to another. Use when Codex needs to (1) create symbolic links between directories, (2) share skills folders across AI tools (e.g., .claude/skills → .skills), (3) move large folders to another drive while keeping original path accessible, or (4) execute mklink commands with proper elevation handling. Use when this capability is needed.
metadata:
  author: zinzan48
---

# Windows Folder Linker

Create Windows symbolic links with automatic permission handling.

## Quick Start

Run the GUI application:

```powershell
cd d:\Project\WindowsFolderLinker
dotnet run
```

## Core Workflow

1. Enter the **Link Path** (the new symlink to create)
2. Enter the **Target Path** (existing folder to point to)
3. Click **建立符號連結**
4. Application auto-detects if elevation needed and uses `sudo` if available

## Permission Handling Logic

```
┌─────────────────────────────────┐
│ Check if running as admin?      │
├─────────────────────────────────┤
│ Yes → Create directly           │
│ No  → Check Developer Mode      │
│       ├─ Enabled → Create       │
│       └─ Disabled → Use sudo    │
│           ├─ Available → sudo   │
│           └─ Not available      │
│               → Show error      │
└─────────────────────────────────┘
```

## PowerShell Alternative

```powershell
# Windows 11 with sudo enabled
sudo cmd /c mklink /D "C:\Users\Name\.claude\skills" "C:\Users\Name\.skills"

# Traditional (requires admin terminal)
New-Item -ItemType SymbolicLink -Path "LinkPath" -Target "TargetPath"
```

## Common Use Cases

### Share skills across AI tools
```
Link:   C:\Users\Name\.claude\skills
Target: C:\Users\Name\.skills
```

### Move folder to another drive
```
Link:   C:\Users\Name\Documents\LargeFolder
Target: D:\Data\LargeFolder
```

## History Storage

Successful operations are saved to:
`%LOCALAPPDATA%\WindowsFolderLinker\history.json`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zinzan48) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
