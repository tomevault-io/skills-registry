---
name: doc-generator
version: 1.8.1
description: Generates project documentation (CLAUDE.md and README.md) with SDLC Agêntico signature
author: SDLC Agêntico Team
created: 2026-01-23
updated: 2026-01-23
status: active
type: utility
invocation: manual
---

# Doc Generator Skill

## Purpose

Automatically generates professional documentation for projects by analyzing the codebase structure, detecting languages and frameworks, and creating:
- `CLAUDE.md` - Guidance for Claude Code
- `README.md` - Project documentation

Both files include the SDLC Agêntico signature: `🤖 Generated with SDLC Agêntico by @arbgjr`

## Features

- **Language Detection**: Auto-detects Python, JavaScript, TypeScript, Java, C#, Go, Rust, Ruby
- **Framework Detection**: Identifies Django, Flask, FastAPI, React, Next.js, Vue, Angular, Express, .NET, Maven, Gradle
- **Directory Analysis**: Generates project structure tree (max 3 levels deep)
- **Smart Defaults**: Language-specific installation, run, and test commands
- **Test Detection**: Identifies test files and directories
- **Docker/CI Detection**: Detects Dockerfile and GitHub Actions
- **Professional Templates**: Pre-built templates with all standard sections
- **Signature**: Adds SDLC Agêntico attribution to all generated docs

## Usage

### Via Slash Command

```bash
/doc-generate
```

### Via Python Script

```bash
# Generate in current directory
python3 .claude/skills/doc-generator/scripts/generate_docs.py

# Generate in specific directory
python3 .claude/skills/doc-generator/scripts/generate_docs.py --output-dir /path/to/project

# Force overwrite existing files
python3 .claude/skills/doc-generator/scripts/generate_docs.py --force
```

## Generated Content

### CLAUDE.md Sections

- Project Overview
- Architecture
- Directory Structure
- Development Setup
- Development Workflow
- Code Standards
- Testing Strategy
- Deployment
- Common Tasks
- Troubleshooting
- **SDLC Agêntico Signature**

### README.md Sections

- Overview
- Features
- Tech Stack
- Getting Started
- Usage
- API Documentation
- Architecture
- Development
- Deployment
- Contributing
- License
- Support
- **SDLC Agêntico Signature**

## Detection Logic

### Languages

Detected by file extensions:
- Python: `*.py`
- JavaScript: `*.js`, `*.jsx`
- TypeScript: `*.ts`, `*.tsx`
- Java: `*.java`
- C#: `*.cs`
- Go: `*.go`
- Rust: `*.rs`
- Ruby: `*.rb`

### Frameworks

Detected by dependency files:
- **Python**: `requirements.txt` → Django, Flask, FastAPI
- **JavaScript/TypeScript**: `package.json` → React, Next.js, Vue, Angular, Express
- **.NET**: `*.csproj` files
- **Java**: `pom.xml` (Maven), `build.gradle` (Gradle)

### Tests

Detected by:
- Test file patterns: `test_*.py`, `*_test.py`, `*.test.js`, `*.spec.js`
- Test directories: `tests/`, `test/`, `__tests__/`, `spec/`

## Templates

Templates are located in `.claude/skills/doc-generator/templates/`:
- `CLAUDE.md.template` - Claude Code guidance template
- `README.md.template` - Project README template

Templates use `{{placeholder}}` syntax for variable substitution.

## Signature Format

All generated files end with:

```markdown
---

🤖 *Generated with [SDLC Agêntico](https://github.com/arbgjr/sdlc_agentico) by [@arbgjr](https://github.com/arbgjr)*
```

This provides:
- ✅ Attribution to SDLC Agêntico
- ✅ Link to project repository
- ✅ Link to author GitHub profile
- ✅ Professional branding

## Integration

### Agents

This skill can be invoked by:
- **doc-generator** agent (Phase 7 - Release)
- **orchestrator** agent (on-demand)
- Manual invocation via `/doc-generate` command

### Phase

Typically used in **Phase 7 (Release)** but can be invoked at any time.

## Dependencies

- Python 3.11+
- `.claude/lib/python/sdlc_logging.py` (structured logging)

## Logging

All operations are logged with:
- **Skill**: `doc-generator`
- **Phase**: 7 (Release)
- **Events**: Project analysis, file generation, errors

View logs:
```bash
# Grafana query
{skill="doc-generator"} | json
```

## Examples

### Python Project

Input:
- `requirements.txt` with `django`
- `tests/` directory
- `Dockerfile`

Output:
- CLAUDE.md with Django-specific setup
- README.md with Django stack
- Run command: `python manage.py runserver`
- Test command: `pytest`

### TypeScript/React Project

Input:
- `package.json` with `react` and `typescript`
- `__tests__/` directory
- `.github/workflows/`

Output:
- CLAUDE.md with React/TS setup
- README.md with React stack
- Run command: `npm start`
- Test command: `npm test`
- Mentions CI automation

## Limitations

- Placeholder content for features, usage examples (requires manual editing)
- Generic architecture descriptions (requires customization)
- Max 3 levels deep for directory structure
- Excludes common directories (node_modules, .git, venv, etc.)

## Future Enhancements

- [ ] Extract actual project description from git commits
- [ ] Parse existing README/package.json for project metadata
- [ ] Generate API docs from code comments
- [ ] Create architecture diagrams (Mermaid)
- [ ] Support for more languages (PHP, Swift, Kotlin)
- [ ] Customizable templates per project type

## Version History

### v1.8.1 (2026-01-23)
- Initial implementation
- Language and framework detection
- Directory structure analysis
- CLAUDE.md and README.md generation
- SDLC Agêntico signature

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arbgjr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
