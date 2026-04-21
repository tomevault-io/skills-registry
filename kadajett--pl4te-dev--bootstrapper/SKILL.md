---
name: bootstrapper
description: Bootstrap new projects with modern tech stacks. Use when creating new applications, setting up projects, initializing codebases, or when the user mentions project setup, scaffolding, or bootstrapping. Supports TanStack Start + shadcn (via npx create-tanstack-start-shadcn), Rust, Tauri, TanStack Router, Python, and C# .NET projects. Use when this capability is needed.
metadata:
  author: kadajett
---

# Project Bootstrapper

A comprehensive project scaffolding system that helps you create new applications with modern, production-ready configurations.

## Supported Tech Stacks

| Stack | Command | Use Case |
|-------|---------|----------|
| **TanStack Start + shadcn** | `npx create-tanstack-start-shadcn my-app` | Full-stack React with SSR, React Query, shadcn/ui, Tailwind v4 |
| **Rust** | `cargo new my-app` | CLI tools, libraries, high-performance applications |
| **Tauri** | `npm create tauri-app@latest` | Cross-platform desktop applications |
| **TanStack Router** | `npx create-tanstack-app@latest` | Modern React SPAs with file-based routing |
| **Python venv** | `uv init my-app` or `python3 -m venv` | Scripts, APIs, data science, automation |
| **C# .NET** | `dotnet new web -n my-app` | APIs, desktop apps, enterprise software |

## Installation Commands

### TanStack Start + shadcn/ui (Recommended for full-stack React)

```bash
# Create a new project
npx create-tanstack-start-shadcn my-app

# With specific package manager
npx create-tanstack-start-shadcn my-app --use-pnpm
npx create-tanstack-start-shadcn my-app --use-yarn
npx create-tanstack-start-shadcn my-app --use-bun

# Start development
cd my-app
npm run dev
```

**Includes:** TanStack Start, TanStack Router, React Query, shadcn/ui (new-york style), Tailwind CSS v4, TypeScript, sidebar layout, server functions, API routes, deferred data loading.

**After installation:** The project includes a `.claude/skills/tanstack-start-shadcn/` directory with comprehensive development documentation.

### Other Stacks

See [TEMPLATES.md](TEMPLATES.md) for detailed commands for all supported stacks.

## How to Use

When asked to bootstrap a project, I will:

1. **Clarify requirements**: Ask about project name, target directory, and specific needs
2. **Check prerequisites**: Verify required tools are installed (Node.js 18+, git, etc.)
3. **Run the scaffolding command**: Execute the appropriate npx/cargo/dotnet command
4. **Verify setup**: Ensure dependencies installed and dev server starts
5. **Point to development docs**: Reference the skill documentation included in the project

## Quick Commands

To bootstrap a project, ask me to:
- "Create a full-stack React app with TanStack Start" → `npx create-tanstack-start-shadcn`
- "Set up a new project with shadcn/ui and Tailwind" → `npx create-tanstack-start-shadcn`
- "Create a new Rust CLI project" → `cargo new`
- "Set up a Tauri desktop app with React" → `npm create tauri-app@latest`
- "Bootstrap a TanStack Router project" → `npx create-tanstack-app@latest`
- "Initialize a Python project with virtual environment" → `uv init` or `python3 -m venv`
- "Create a new .NET 8 Web API" → `dotnet new web`

## Prerequisites Check

Before bootstrapping, I'll verify these tools are available:

```bash
# Check common tools
node --version  # Should be 18+
which git && git --version
```

For stack-specific requirements, see [TEMPLATES.md](TEMPLATES.md).

## Project Naming Conventions

- Use lowercase with hyphens: `my-awesome-project`
- Avoid spaces and special characters
- Keep names concise but descriptive

## Post-Setup Recommendations

After bootstrapping, I'll suggest:
- Setting up version control (if not already a git repo)
- Configuring CI/CD pipelines
- Adding appropriate .gitignore entries
- Setting up pre-commit hooks
- Configuring IDE/editor settings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kadajett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
