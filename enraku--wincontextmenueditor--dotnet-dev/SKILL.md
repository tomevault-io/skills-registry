---
name: dotnet-dev
description: .NET 8 C# WPF project build, run, and development workflow. Use when building, running, testing, or adding dependencies to this .NET project. Triggers on: dotnet build errors, adding NuGet packages, running the app, creating new classes/files in the project structure. Use when this capability is needed.
metadata:
  author: enraku
---

# .NET Development Workflow

## Project Layout

```
WinContextMenuEditor/
├── WinContextMenuEditor.sln
└── src/WinContextMenuEditor/
    ├── WinContextMenuEditor.csproj  (.NET 8, WPF, UseWPF=true)
    ├── app.manifest                 (requireAdministrator)
    ├── Models/
    ├── ViewModels/
    ├── Views/
    ├── Services/
    ├── Converters/
    └── Resources/
```

## Build & Run

```bash
# Build
dotnet build

# Run (requires admin - UAC prompt will appear)
dotnet run --project src/WinContextMenuEditor

# Publish self-contained
dotnet publish src/WinContextMenuEditor -c Release -r win-x64 --self-contained
```

## Adding New Files

Follow existing namespace conventions:
- Models: `namespace WinContextMenuEditor.Models;`
- ViewModels: `namespace WinContextMenuEditor.ViewModels;` - inherit `ViewModelBase`
- Views: `namespace WinContextMenuEditor.Views;` - XAML + code-behind
- Services: `namespace WinContextMenuEditor.Services;` - interface + implementation pair
- Converters: `namespace WinContextMenuEditor.Converters;` - implement `IValueConverter`

Use file-scoped namespaces. Use nullable reference types. Target .NET 8.

## Common Build Errors

- **MC3024 property already set**: XAML element has both attribute and element syntax for same property. Remove one.
- **NETSDK1031 platform mismatch**: Ensure `net8.0-windows` target framework.
- **Missing WPF types**: Verify `<UseWPF>true</UseWPF>` in csproj.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enraku) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
