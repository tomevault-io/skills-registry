---
name: document-readme-analyze
description: Analyze project structure, tech stack, and entry points to collect information needed for README.md generation. Parse directory layout, config files such as package.json and pyproject.toml, existing docs, and CI/CD settings. Use when asked to "analyze the project", "collect README inputs", "investigate the tech stack", or analyze a project for documentation. Use when this capability is needed.
metadata:
  author: luckgakidz
---

# Project Analysis Skill

This skill analyzes project source code and configuration files to systematically gather information needed to generate or update README.md.

## When to Use This Skill

- Gathering information before creating README.md for a new project
- Understanding changes needed when updating an existing README.md
- Understanding project tech stack and architecture

## Prerequisites

- Access to the target project's source code

## Step-by-Step Workflows

### Workflow 1: Full Project Analysis

1. **Understand directory structure**
   - Use `#codebase` to inspect full file/directory layout
   - Check key files in the root directory

2. **Analyze package manager and config files**
   - Search/read the following files:
     - `package.json` / `package-lock.json` / `yarn.lock` / `pnpm-lock.yaml`
     - `pyproject.toml` / `setup.py` / `requirements.txt` / `Pipfile`
     - `go.mod` / `Cargo.toml` / `pom.xml` / `build.gradle`
     - `Gemfile` / `composer.json` / `.csproj`
   - Extract project name, version, description, and dependencies

3. **Identify tech stack**
   - Languages (extension-based + config-based)
   - Frameworks (React, Next.js, Express, Django, FastAPI, etc.)
   - Test frameworks (Jest, Pytest, Vitest, Playwright, etc.)
   - Build tools (Webpack, Vite, esbuild, tsc, etc.)
   - Linters/formatters (ESLint, Prettier, Ruff, Black, etc.)

4. **Identify entry points**
   - Parse `main` field / `scripts` section
   - Search common patterns like `src/index.*`, `src/main.*`, `app.*`
   - Identify CLI commands / API endpoints / UI entry points

5. **Check CI/CD and deployment setup**
   - `.github/workflows/` - GitHub Actions
   - `.gitlab-ci.yml` - GitLab CI
   - `Dockerfile` / `docker-compose.yml` - Docker
   - `vercel.json` / `netlify.toml` - deployment configs

6. **Check existing documentation**
   - `README.md` - existing README
   - `CONTRIBUTING.md` - contribution guide
   - `CHANGELOG.md` - change history
   - `LICENSE` - license
   - `docs/` directory - additional docs

7. **Check environment variables and settings**
   - `.env.example` / `.env.sample`
   - Collect environment variable list and descriptions

### Workflow 2: Diff-Based Analysis (for updates)

1. Check git diffs with `#changes`
2. Determine whether changes affect README:
   - Added/removed dependencies
   - Script command changes
   - Directory structure changes
   - New features / APIs
   - Configuration changes
3. Identify impacted README sections

## Output Format

Output analysis results as structured data:

```markdown
## Project Analysis Results

### Basic Information
- **Project Name**: [name]
- **Version**: [version]
- **Description**: [description]
- **License**: [license]
- **Repository**: [repository URL]

### Tech Stack
| Category | Technologies |
|----------|--------------|
| Languages | [languages] |
| Frameworks | [frameworks] |
| Testing | [testing tools] |
| Build | [build tools] |
| CI/CD | [CI/CD tools] |

### Directory Structure
[tree structure]

### Scripts / Commands
| Command | Description |
|---------|-------------|
| `npm run dev` | Start dev server |
| ... | ... |

### Environment Variables
| Variable | Description | Required |
|----------|-------------|:--------:|
| `API_KEY` | API key | ✅ |
| ... | ... | ... |

### Impacted README Sections (updates only)
- [ ] Installation
- [ ] Usage
- [ ] API reference
- [ ] Environment variables
```

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Unknown package manager | Infer from lock files (`yarn.lock` -> Yarn, etc.) |
| Monorepo layout | Check `workspaces` / `packages/` and analyze each package |
| Missing config files | Infer dependencies from import/require usage in source code |
| Mixed languages | Determine primary language by file count and LoC |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luckgakidz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
