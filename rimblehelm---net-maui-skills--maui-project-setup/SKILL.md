---
name: maui-project-setup
description: Use when working with a brief description of what this skill does
metadata:
  author: rimblehelm
---

# .NET MAUI — Project Setup Skill

## Purpose

This skill provides agents with a standardized, production-ready approach to creating and structuring .NET MAUI applications. It defines folder conventions, MVVM architecture defaults, dependency injection patterns, and cross-platform considerations that should be applied whenever a new MAUI project is initialized.

The goal is to ensure that all generated MAUI projects follow consistent, maintainable, and scalable patterns.

## Core Principles

1. **MVVM-first architecture**
   Use ViewModels, observable properties, and commands as the primary interaction pattern.

2. **Dependency Injection**
   Register services in `MauiProgram.cs` using the built-in DI container.

3. **Separation of Concerns**
   Keep UI, business logic, and data access isolated in clear folder structures.

4. **Cross-platform awareness**
   Prefer abstractions over platform-specific code unless required.

5. **Scalable folder structure**
   Organize code into predictable, discoverable modules.

## Recommended Folder Structure

```text
[root]
├─ Models
│  └─ [Add model classes here]
├─ Platforms
│  └─ [Add platform-specific setup and code here]
├─ Resources
│  ├─ AppIcon
│  │  └─ [Add application icon files here]
│  ├─ Fonts
│  │  └─ [Add font files here]
│  ├─ Images
│  │  └─ [Add image files here]
│  ├─ raw
│  │  └─ [Add raw files here]
│  ├─ Splash
│  │  └─ [Add splash screen files here]
│  └─ Styles
│     └─ [Add style files here]
├─ Services
│  ├─ Interfaces
│  │  └─ [Add service interfaces here]
│  └─ [Add service classes here]
├─ ViewModels
│  ├─ MainViewModel.cs
│  └─ [Add other ViewModels here]
├─ Views
│  ├─ Controls
│  │  └─ [Add custom controls here]
│  ├─ MainPage.xaml
│  │  └─ MainPage.xaml.cs
│  └─ Templates
│    └─ [Add custom templates here]
├─ App.xaml
│  └─ App.xaml.cs
└─ MauiProgram.cs
```

## Project Initialization Steps

1. Create a new MAUI project:

   ```code
   dotnet new maui -n [ProjectName]
   ```

2. Add MVVM base classes:
   - `BaseViewModel`
   - `ObservableObject` or `INotifyPropertyChanged` implementation
   - `AsyncCommand` helpers

3. Register services in `MauiProgram.cs`:

   ```code
   builder.Services.AddSingleton<INavigationService, NavigationService>();
   builder.Services.AddTransient<MainViewModel>();
   builder.Services.AddTransient<MainPage>();
   ```

4. Configure global styles in `Resources/Styles/Styles.xaml`.
5. Add platform assets:
   - App icons
   - Splash screens
   - Permissions manifests

## Agent Usage Guidelines

- Always scaffold new MAUI apps using the folder structure above (Change it after project creation).
- When generating code, place files in the correct folders.
- When asked to “create a new page,” generate both:
  - `Views/MyPage.xaml`
  - `ViewModels/MyPageViewModel.cs`
- When asked to “add a service,” create:
  - `Services/Interfaces/IServiceName.cs`
  - `Services/ServiceName.cs`
- Register it in `MauiProgram.cs`.

## Out of Scope

- Deep UI best practices (covered in `maui-ui-best-practices`)
- Authentication flows (covered in `maui-authentication`)
- Deployment (covered in `maui-deployment`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rimblehelm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
