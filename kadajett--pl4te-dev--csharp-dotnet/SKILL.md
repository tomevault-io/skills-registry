---
name: csharp-dotnet
description: Bootstrap C# .NET projects with modern tooling. Use when creating .NET applications, Web APIs, console apps, Blazor apps, or when the user wants to set up a C# development environment. Use when this capability is needed.
metadata:
  author: kadajett
---

# C# .NET Project Bootstrapper

Creates production-ready .NET 8+ projects with modern C# features and best practices.

## Prerequisites

```bash
# Check .NET SDK (8.0+ recommended)
dotnet --version
dotnet --list-sdks
```

### Install .NET SDK

**Linux (Ubuntu/Debian)**:
```bash
wget https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
rm packages-microsoft-prod.deb
sudo apt-get update
sudo apt-get install -y dotnet-sdk-8.0
```

**macOS**:
```bash
brew install --cask dotnet-sdk
```

## Project Templates

| Template | Command | Description |
|----------|---------|-------------|
| Console App | `dotnet new console` | Command-line application |
| Web API | `dotnet new webapi` | REST API with controllers |
| Minimal API | `dotnet new web` | Lightweight HTTP API |
| Blazor Server | `dotnet new blazorserver` | Server-side Blazor app |
| Blazor WebAssembly | `dotnet new blazorwasm` | Client-side Blazor app |
| Class Library | `dotnet new classlib` | Reusable library |
| xUnit Tests | `dotnet new xunit` | Unit test project |
| Worker Service | `dotnet new worker` | Background service |

## Create Projects

### Console Application

```bash
dotnet new console -n MyApp -o MyApp
cd MyApp
```

### Web API (Minimal API - Recommended)

```bash
dotnet new web -n MyApi -o MyApi
cd MyApi
```

### Solution with Multiple Projects

```bash
dotnet new sln -n MySolution
dotnet new web -n MyApi -o src/MyApi
dotnet new classlib -n MyCore -o src/MyCore
dotnet new xunit -n MyTests -o tests/MyTests
dotnet sln add src/MyApi src/MyCore tests/MyTests
dotnet add src/MyApi reference src/MyCore
dotnet add tests/MyTests reference src/MyCore
```

## Project Structure

```
MySolution/
├── MySolution.sln
├── src/
│   ├── MyApi/
│   │   ├── MyApi.csproj
│   │   └── Program.cs
│   └── MyCore/
│       └── MyCore.csproj
├── tests/
│   └── MyTests/
│       └── MyTests.csproj
├── .editorconfig
├── Directory.Build.props
└── Directory.Packages.props
```

## Modern .csproj Configuration

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
    <AnalysisLevel>latest-recommended</AnalysisLevel>
    <EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>
  </PropertyGroup>
</Project>
```

## Directory.Build.props

Create at solution root:

```xml
<Project>
  <PropertyGroup>
    <TargetFramework>net8.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
    <AnalysisLevel>latest-recommended</AnalysisLevel>
  </PropertyGroup>
</Project>
```

## Minimal API Example

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.MapGet("/", () => "Hello World!");
app.MapGet("/items/{id}", (int id) => new Item(id, $"Item {id}"));
app.MapPost("/items", (Item item) => Results.Created($"/items/{item.Id}", item));

app.Run();

record Item(int Id, string Name);
```

## Common NuGet Packages

```bash
dotnet add package Serilog.AspNetCore
dotnet add package FluentValidation.AspNetCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package xunit
dotnet add package Moq
dotnet add package FluentAssertions
```

## Development Commands

```bash
dotnet restore
dotnet build
dotnet run
dotnet watch run          # Hot reload
dotnet test
dotnet format
dotnet publish -c Release -o ./publish
```

## GitHub Actions CI

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: 8.0.x
      - run: dotnet restore
      - run: dotnet build --no-restore -c Release
      - run: dotnet test --no-build -c Release
```

## Post-Setup Checklist

1. [ ] Create solution and project structure
2. [ ] Configure Directory.Build.props
3. [ ] Add .editorconfig for code style
4. [ ] Configure logging (Serilog)
5. [ ] Set up dependency injection
6. [ ] Add unit test project
7. [ ] Configure CI/CD pipeline

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kadajett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
