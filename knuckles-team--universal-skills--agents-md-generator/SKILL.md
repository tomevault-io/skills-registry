---
name: agents-md-generator
description: >- Use when this capability is needed.
metadata:
  author: Knuckles-Team
---

# AGENTS.md Generator

Generates a robust `AGENTS.md` file designed specifically for AI coding agents. This file acts as a "README for machines," providing them with the exact project context, rules, and instructions they need to work effectively.

## Features
- **Project Metadata Extraction**: Automatically parses `pyproject.toml` for project name, description, and scripts.
- **Dynamic File Tree**: Generates a text-based tree view of the project structure, ignoring common noise directories (`.git`, `__pycache__`, etc.).
- **Mermaid Diagrams**: Includes standardized Architecture and Workflow diagrams to help agents understand the system's design.
- **Battle-Tested Template**: Uses a structure optimized for high agent performance, including "Always/Ask first/Never" boundaries.

## Usage

Run the generator script with the path to your project root:

```bash
python scripts/generate_agents_md.py <project-path>
```

### Options
- `<project-path>`: (Required) Path to the root directory of the project to document.

## AGENTS.md Standard

The generated `AGENTS.md` follows the emerging open standard for AI agent documentation:
1. **Tech Stack & Architecture**: High-level overview of technologies and component interactions.
2. **Commands**: Exact, file-scoped commands for building, testing, and linting.
3. **Project Structure**: Quick reference to key files and the dynamic tree view.
4. **Code Style & Conventions**: Specific "Always" rules with concrete Good/Bad code examples.
5. **Dos and Don'ts**: Specific behavioral guidelines.
6. **Safety & Boundaries**: Explicit "Always do", "Ask first", and "Never do" constraints.
7. **When Stuck**: Instructions on how to handle ambiguity (e.g., "propose a plan first").

---
> Source: [Knuckles-Team/universal-skills](https://github.com/Knuckles-Team/universal-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
