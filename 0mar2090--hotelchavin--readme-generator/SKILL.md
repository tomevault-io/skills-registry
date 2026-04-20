---
name: readme-generator
description: Generate professional, comprehensive README.md files for projects. Analyzes project files, dependencies, and structure to create detailed documentation including features, installation instructions, tech stack, screenshots, and usage examples. Trigger when users ask to "improve README", "generate README", "create documentation", "document this project", or "make a better README". Use when this capability is needed.
metadata:
  author: 0mar2090
---

# README Generator

Generate professional README.md files by analyzing the project structure and contents.

## Process

### Step 1: Analyze Project

Scan these files to understand the project:

1. **Package files**: `package.json`, `requirements.txt`, `Cargo.toml`, `go.mod`, etc.
2. **Config files**: `vite.config.js`, `webpack.config.js`, `tsconfig.json`, `.env.example`
3. **Source structure**: Main entry points, component folders, key modules
4. **Existing docs**: Current README.md, CHANGELOG.md, docs/

### Step 2: Extract Key Information

From the analysis, identify:

- **Project name** (from package.json or folder name)
- **Description** (from package.json description or infer from code)
- **Tech stack** (frameworks, libraries, tools)
- **Key features** (main components, routes, functionality)
- **Scripts/Commands** (npm scripts, make targets)
- **Configuration** (env vars, settings)
- **Dependencies** (main ones worth mentioning)

### Step 3: Generate README Structure

Use this template structure (adapt as needed):

```markdown
# Project Name

Brief, compelling description (1-2 sentences)

![Demo/Screenshot](link-to-image-or-gif)

## Features

- Feature 1 with brief explanation
- Feature 2 with brief explanation
- Feature 3 with brief explanation

## Tech Stack

| Category | Technology                  |
| -------- | --------------------------- |
| Frontend | React, Tailwind             |
| 3D       | Three.js, React Three Fiber |
| Build    | Vite                        |

## Quick Start

### Prerequisites

- Node.js >= 18
- npm or yarn

### Installation

\`\`\`bash
git clone https://github.com/user/repo.git
cd repo
npm install
npm run dev
\`\`\`

## Project Structure

\`\`\`
src/
components/ # React components
context/ # State management
assets/ # Static files
public/
models/ # 3D models (GLB)
images/ # Images
\`\`\`

## Scripts

| Command           | Description              |
| ----------------- | ------------------------ |
| `npm run dev`     | Start development server |
| `npm run build`   | Build for production     |
| `npm run preview` | Preview production build |

## Configuration

Describe any .env or config options here.

## Contributing

1. Fork the repo
2. Create feature branch
3. Commit changes
4. Push to branch
5. Open PR

## License

MIT License - see LICENSE for details
```

### Step 4: Enhance with Context

Add project-specific sections:

- **For 3D projects**: Add "3D Models" section explaining GLB files
- **For API projects**: Add "API Endpoints" table
- **For component libraries**: Add "Components" section
- **For apps with auth**: Add "Authentication" section

### Step 5: Add Visual Elements

When possible, include:

1. **Badges** (shields.io) for version, license, build status
2. **Screenshots** or GIFs showing the app
3. **Tables** for structured data
4. **Mermaid diagrams** for architecture
5. **Emojis** for visual anchors (use sparingly)

## Best Practices

1. **First paragraph sells** - Make description compelling
2. **Quick start in < 60 seconds** - Users should run app fast
3. **Show, don't tell** - Screenshots > paragraphs
4. **Keep current** - Outdated docs are worse than none
5. **Link to details** - Don't bloat README, link to docs/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/0mar2090) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
