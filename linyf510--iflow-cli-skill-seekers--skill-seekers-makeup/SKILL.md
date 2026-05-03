---
name: skill-seekers-makeup
description: Automatically generate Claude skills from documentation websites, GitHub repositories, and PDF files using the Skill_Seekers tool. **ALWAYS use this skill when:** Use when this capability is needed.
metadata:
  author: linyf510
---

# Skill Seekers Makeup

This skill automates the creation of Claude skills from various content sources using the Skill_Seekers tool.

## When to Use

Use this skill when:
- User wants to create a new skill from documentation websites
- User wants to create a new skill from GitHub repositories
- User wants to create a new skill from PDF documents
- User wants to create a new skill combining multiple sources (docs + GitHub + PDF)
- User wants to update an existing skill with new content

## Quick Start

### Installation Location

By default, generated skills are installed to the project root's `.iflow/skills/` directory. To install skills to the global `~/.iflow/skills/` directory, use the `--global` flag.

### Basic Usage

Use the `scripts/generate_skill.py` script to generate skills:

```bash
# Generate skill from documentation website
python scripts/generate_skill.py \
  --name react \
  --description "React framework documentation" \
  --urls https://react.dev

# Generate skill from GitHub repository
python scripts/generate_skill.py \
  --name typescript \
  --description "TypeScript programming language" \
  --repos microsoft/TypeScript

# Generate skill from PDF documents
python scripts/generate_skill.py \
  --name manual \
  --description "Product manual" \
  --pdfs docs/manual.pdf

# Generate skill from multiple sources
python scripts/generate_skill.py \
  --name my-framework \
  --description "Complete framework knowledge" \
  --urls https://docs.myframework.com \
  --repos owner/myframework \
  --pdfs docs/advanced.pdf

# Generate skill with smart path-aware scraping
python scripts/generate_skill.py \
  --name iflow-cli \
  --description "iFlow CLI documentation" \
  --urls https://platform.iflow.cn/cli/quickstart \
  --smart-scrape
```

### Updating an Existing Skill

To update a skill with new content:

```bash
python scripts/generate_skill.py \
  --name existing-skill \
  --update \
  --urls https://new-docs.com \
  --repos owner/new-repo
```

## Parameters

### Required Parameters

- `--name <name>`: Name of the skill to generate (used for installation path)
- `--description <description>`: Description of what the skill does and when to trigger it

### Content Sources (at least one required)

- `--urls <url1,url2,...>`: Comma-separated list of documentation website URLs
- `--repos <repo1,repo2,...>`: Comma-separated list of GitHub repositories (format: owner/repo)
- `--pdfs <pdf1,pdf2,...>`: Comma-separated list of PDF file paths

### Optional Parameters

- `--update`: Update an existing skill instead of creating a new one
- `--async`: Enable async scraping mode for faster processing (2-3x speedup)
- `--workers <n>`: Number of async workers (default: 4)
- `--output-dir <path>`: Output directory for generated skill (default: output/)
- `--skip-enhance`: Skip AI enhancement step (not recommended)
- `--skip-package`: Skip packaging step (keeps raw output)
- `--skip-install`: Skip installation to .iflow/skills/
- `--global`: Install skill to global ~/.iflow/skills/ directory (default: install to project root's .iflow/skills/)
- `--verbose`: Enable verbose logging
- `--smart-scrape`: Use smart path-aware crawler (limits to same path level and max 3 hops)

## Workflow

The skill executes the following steps automatically:

### Phase 1: Scrape Content

For each content source:

**Documentation Websites:**
- Uses `skill-seekers scrape` command (default) or smart scraper (with `--smart-scrape`)
- Smart scraper mode: Limits crawling to same path level and max 3 hops from entry URL
  - Example: Entry `https://www.example.com/docs/api`
  - ✅ `https://www.example.com/docs/changelog` (same level)
  - ✅ `https://www.example.com/docs/api/abc` (deeper level)
  - ✅ `https://www.example.com/docs/protocol/def` (same level's subpath)
  - ❌ `https://www.anotherexample.com` (different host)
  - ❌ `https://www.example.com/` (higher level)
  - ❌ `https://www.example.com/blogs` (different path)
- Detects and uses llms.txt if available (10x faster)
- Scrapes all pages from the documentation
- Categorizes content by topic
- Supports async mode with `--async` flag

**GitHub Repositories:**
- Uses `skill-seekers github` command
- Extracts README, code structure, and signatures
- Fetches GitHub Issues and PRs
- Extracts CHANGELOG and Releases
- Performs deep code analysis with AST parsing

**PDF Documents:**
- Uses `skill-seekers pdf` command
- Extracts text, code, and images
- Supports OCR for scanned PDFs
- Handles password-protected PDFs
- Extracts tables from complex layouts

### Phase 2: AI Enhancement

- Uses `skill-seekers enhance` command
- Enhances SKILL.md with comprehensive examples
- Extracts best practices and key concepts
- Improves documentation quality
- **Note:** Enhancement is mandatory for production-ready skills

### Phase 3: Package Skill

- Uses `skill-seekers package` command
- Creates Claude-ready .skill package
- Validates skill structure
- Ensures quality standards
- Creates distributable skill file

### Phase 4: Install Skill

- Installs to project root's `.iflow/skills/<skill-name>/` by default
- Installs to global `~/.iflow/skills/<skill-name>/` when `--global` flag is used
- Preserves existing skill content when updating
- Maintains proper directory structure
- Ready for immediate use

## Examples

### Example 1: Create React Skill

```bash
python scripts/generate_skill.py \
  --name react \
  --description "React framework for building user interfaces. Use when working with React components, hooks, state management, or React-specific patterns." \
  --urls https://react.dev \
  --async \
  --workers 8
```

### Example 2: Create Django Skill with GitHub

```bash
python scripts/generate_skill.py \
  --name django \
  --description "Django web framework. Use when building Django applications, working with models, views, templates, or Django ORM." \
  --urls https://docs.djangoproject.com \
  --repos django/django \
  --async
```

### Example 3: Create PDF-Based Skill

```bash
python scripts/generate_skill.py \
  --name api-reference \
  --description "REST API reference documentation. Use when working with API endpoints, authentication, or data models." \
  --pdfs docs/api-reference.pdf
```

### Example 4: Multi-Source Skill

```bash
python scripts/generate_skill.py \
  --name godot \
  --description "Godot game engine. Use when developing games with Godot, working with GDScript, scenes, nodes, or Godot-specific features." \
  --urls https://docs.godotengine.org \
  --repos godotengine/godot \
  --pdfs docs/godot-manual.pdf \
  --async \
  --workers 8
```

### Example 5: Update Existing Skill

```bash
python scripts/generate_skill.py \
  --name react \
  --update \
  --urls https://react.dev,https://react-redux.js.org \
  --repos facebook/react
```

### Example 6: Install Skill to Global Directory

```bash
python scripts/generate_skill.py \
  --name react \
  --description "React framework for building user interfaces" \
  --urls https://react.dev \
  --global
```

## Advanced Usage

### Using Custom Config Files

For advanced configurations, create a custom Skill_Seekers config file:

```bash
# Create custom config
cat > my-config.json << EOF
{
  "name": "my-framework",
  "description": "My custom framework",
  "base_url": "https://docs.myframework.com",
  "max_pages": 500,
  "async": true,
  "workers": 8
}
EOF

# Use custom config
python scripts/generate_skill.py \
  --name my-framework \
  --description "My custom framework" \
  --config my-config.json
```

### Performance Optimization

For large documentation sets (10K+ pages):

```bash
python scripts/generate_skill.py \
  --name large-docs \
  --description "Large documentation set" \
  --urls https://docs.example.com \
  --async \
  --workers 16 \
  --verbose
```

### Batch Processing

Generate multiple skills at once:

```bash
# Create a batch script
cat > batch-generate.sh << EOF
#!/bin/bash

python scripts/generate_skill.py \
  --name react \
  --description "React framework" \
  --urls https://react.dev

python scripts/generate_skill.py \
  --name vue \
  --description "Vue.js framework" \
  --urls https://vuejs.org

python scripts/generate_skill.py \
  --name angular \
  --description "Angular framework" \
  --urls https://angular.io
EOF

# Run batch
chmod +x batch-generate.sh
./batch-generate.sh
```

## Troubleshooting

### Skill_Seekers Not Found

If you get "command not found: skill-seekers":

```bash
# Install Skill_Seekers
pip install skill-seekers

# Or install from source
cd /path/to/Skill_Seekers
pip install -e .
```

### GitHub Rate Limiting

If you hit GitHub rate limits:

```bash
# Set GitHub token
export GITHUB_TOKEN=ghp_your_token_here

# Retry the command
python scripts/generate_skill.py --name my-skill --repos owner/repo
```

### Async Mode Issues

If async mode fails:

```bash
# Try without async mode
python scripts/generate_skill.py --name my-skill --urls https://docs.com --no-async
```

### Enhancement Fails

If AI enhancement fails:

```bash
# Check Claude Code Max is available
claude --version

# Or skip enhancement (not recommended)
python scripts/generate_skill.py --name my-skill --urls https://docs.com --skip-enhance
```

## Best Practices

1. **Always provide a clear description** that includes when to trigger the skill
2. **Use async mode for large documentation** to speed up processing
3. **Combine multiple sources** when possible for comprehensive coverage
4. **Test generated skills** before deployment
5. **Keep skill names descriptive and unique**
6. **Update skills regularly** when documentation changes
7. **Use specific URLs** rather than root URLs when possible

## Output Structure

Generated skills follow this structure:

**Default (project root):**
```
<project-root>/.iflow/skills/<skill-name>/
├── SKILL.md              # Main skill file with metadata and instructions
├── api/                  # API documentation (if applicable)
├── guides/               # User guides and tutorials
├── examples/             # Code examples
├── reference/            # Reference documentation
└── assets/               # Images, diagrams, etc.
```

**Global (with --global flag):**
```
~/.iflow/skills/<skill-name>/
├── SKILL.md              # Main skill file with metadata and instructions
├── api/                  # API documentation (if applicable)
├── guides/               # User guides and tutorials
├── examples/             # Code examples
├── reference/            # Reference documentation
└── assets/               # Images, diagrams, etc.
```

## References

For detailed Skill_Seekers documentation, see:
- [Skill_Seekers README](https://github.com/yusufkaraaslan/Skill_Seekers)
- [Skill_Seekers Commands Reference](references/skill_seekers_commands.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/linyf510) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
