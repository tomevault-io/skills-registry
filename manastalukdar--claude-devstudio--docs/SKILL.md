---
name: docs
description: Smart documentation management for creating and updating project documentation Use when this capability is needed.
metadata:
  author: manastalukdar
---

# Documentation Manager

I'll intelligently manage your project documentation by analyzing what actually happened and updating ALL relevant docs accordingly.

---

## Token Optimization Strategy

**Target: 60% reduction (3,000-5,000 → 1,200-2,000 tokens)**

### Optimization Status
- **Status:** ✅ Optimized (Phase 2 Batch 3B, 2026-01-26)
- **Expected tokens:** 1,200-2,000 (vs. 3,000-5,000 unoptimized)
- **Average reduction:** 60% across all documentation operations
- **Cache hit savings:** 95% when docs are current

### Core Optimization Patterns

**1. Glob-First Documentation Discovery (95% savings)**
```bash
# Discover all documentation without reading content
docs/
├── README.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── docs/
│   ├── API.md
│   ├── architecture.md
│   └── guides/

# Result: 100-200 tokens vs 5,000+ tokens reading all files
```

**2. Git Diff-Driven Documentation Updates (80% savings)**
```bash
# Detect which code changed to target documentation updates
git diff main...HEAD --name-only --diff-filter=AM

# Only read/update docs related to changed code areas:
# - src/api/* changed → Update API.md only
# - tests/* added → Update testing guide
# - No changes → Early exit, save 95%

# Result: 500-1,000 tokens vs 4,000+ tokens updating everything
```

**3. Documentation Framework Detection Caching (70% savings)**
```bash
# Cache detected documentation framework on first run
CACHE_FILE=".claude/cache/docs/framework.json"

{
  "framework": "JSDoc",
  "structure": {
    "api": "docs/API.md",
    "architecture": "docs/architecture.md",
    "changelog": "CHANGELOG.md"
  },
  "conventions": {
    "style": "keep-a-changelog",
    "versioning": "semver"
  },
  "last_checksum": "a1b2c3d4"
}

# Subsequent runs: Read cache (50 tokens) vs analyzing structure (2,000 tokens)
```

**4. Template-Based Documentation Generation (60% savings)**
```bash
# Generate docs from templates rather than analyzing from scratch
TEMPLATES=".claude/cache/docs/templates/"

# Available templates:
# - API endpoint documentation
# - Function/method documentation (JSDoc, Sphinx, GoDoc)
# - Configuration option documentation
# - Migration guide structure
# - Troubleshooting section templates

# Result: 300-500 tokens template application vs 2,000+ tokens generation
```

**5. Incremental Documentation Updates (70% savings)**
```bash
# Update only changed sections, not full regeneration
# Read existing doc → Find section → Update in-place

# Example: Update single API endpoint
Old approach: Regenerate entire API.md (3,000 tokens)
Optimized: Update one section (500 tokens)

# Use Edit tool with precise old_string matching
# Preserve existing content, formatting, custom sections
```

**6. Grep for Undocumented Exports (85% savings)**
```bash
# Find undocumented code without reading all files
# Pattern-based discovery of documentation gaps

# TypeScript/JavaScript
rg "^export (function|class|interface|type)" --glob "src/**/*.ts" \
   | grep -v "@param\|@returns\|/\*\*"

# Python
rg "^def |^class " --glob "**/*.py" \
   | grep -v '"""'

# Result: 200-400 tokens Grep vs 3,000+ tokens reading all source files
```

**7. Early Exit on Current Documentation (95% savings)**
```bash
# Check if documentation needs updating before proceeding
check_docs_freshness() {
    CACHE_FILE=".claude/cache/docs/last-update.json"

    # Compare timestamps and checksums
    LAST_UPDATE=$(jq -r '.timestamp' "$CACHE_FILE")
    LAST_COMMIT=$(git log -1 --format=%ct)

    if [ "$LAST_UPDATE" -ge "$LAST_COMMIT" ]; then
        echo "✓ Documentation is current (updated after last commit)"
        exit 0  # Save 95% tokens
    fi
}

# Result: 100 tokens early exit vs 2,000+ tokens updating
```

**8. Focus Area Flags for Targeted Updates (70% savings)**
```bash
# Update specific documentation areas only
docs --readme        # Update README only (600-1,200 tokens)
docs --changelog     # Update CHANGELOG only (400-800 tokens)
docs --api          # Update API docs only (1,000-2,000 tokens)
docs --architecture # Update architecture docs only (800-1,500 tokens)

# vs full update: 3,000-5,000 tokens
```

### Caching Strategy

**Cache Location:** `.claude/cache/docs/`

**Cached Artifacts:**
```json
{
  "docs/inventory.json": {
    "files": ["README.md", "CHANGELOG.md", "docs/API.md"],
    "structure": { "api": "docs/API.md", "changelog": "CHANGELOG.md" },
    "last_scan": "2026-01-27T10:30:00Z"
  },
  "docs/framework.json": {
    "framework": "JSDoc",
    "conventions": { "style": "keep-a-changelog", "versioning": "semver" },
    "structure_checksum": "a1b2c3d4"
  },
  "docs/last-update.json": {
    "timestamp": 1706352600,
    "updated_files": ["README.md", "docs/API.md"],
    "last_commit": "abc123def"
  },
  "docs/templates/": {
    "api-endpoint.md": "...",
    "function-jsdoc.tpl": "...",
    "changelog-entry.md": "..."
  }
}
```

**Cache Invalidation:**
- **Structure changes:** When new .md files added or removed
- **Code changes:** When git diff shows modified files requiring doc updates
- **Manual updates:** When documentation files manually edited
- **Checksum validation:** Compare package.json/pyproject.toml checksums

**Shared Caches:**
- `/understand` - Project structure and architecture analysis
- `/readme-generate` - README templates and project metadata
- `/changelog-auto` - Version history and commit patterns
- `/inline-docs` - Documentation framework detection (JSDoc/Sphinx/GoDoc)

### Token Budget Breakdown

**Operation: Overview Mode (Default)**
```
├── Check cache validity (50 tokens)
├── Glob documentation files (100 tokens)
├── Read cache inventory (50 tokens)
├── Generate overview report (600 tokens)
└── Total: 800-1,500 tokens (vs 3,500-5,000 unoptimized)
```

**Operation: Smart Update Mode**
```
├── Git diff to detect changes (100 tokens)
├── Check docs freshness cache (50 tokens)
├── Identify affected docs (100 tokens)
├── Read only affected sections (500-1,000 tokens)
├── Apply template-based updates (400-600 tokens)
├── Update cache (50 tokens)
└── Total: 1,200-2,000 tokens (vs 4,000-7,000 unoptimized)
```

**Operation: Focused Update (--readme, --changelog, etc.)**
```
├── Read cache for structure (50 tokens)
├── Read specific file only (200-400 tokens)
├── Apply targeted update (300-500 tokens)
├── Update cache (50 tokens)
└── Total: 600-1,200 tokens (vs 2,000-3,500 unoptimized)
```

**Operation: Early Exit (Docs Current)**
```
├── Check last update timestamp (50 tokens)
├── Compare with git log (50 tokens)
├── Exit with "docs current" message (100 tokens)
└── Total: 200 tokens (95% savings vs full update)
```

### Usage Patterns

**Optimized Usage:**
```bash
# Overview with minimal tokens
docs                    # 800-1,500 tokens (Glob + cache)

# Targeted updates
docs update             # 1,200-2,000 tokens (git diff driven)
docs --readme           # 600-1,200 tokens (README only)
docs --changelog        # 400-800 tokens (CHANGELOG only)
docs --api              # 1,000-2,000 tokens (API docs only)
docs --architecture     # 800-1,500 tokens (architecture only)

# After session work
docs update             # 1,500-2,500 tokens (session context)

# Force refresh (bypass cache)
docs --no-cache         # 3,000-5,000 tokens (full analysis)
```

### Optimization Techniques Summary

| Technique | Token Savings | When Applied |
|-----------|---------------|--------------|
| Glob-first discovery | 95% | All documentation discovery |
| Git diff targeting | 80% | Update operations |
| Framework detection cache | 70% | First-time analysis (cached thereafter) |
| Template-based generation | 60% | Doc creation/updates |
| Incremental updates | 70% | Section modifications |
| Grep undocumented code | 85% | Gap analysis |
| Early exit | 95% | When docs current |
| Focus area flags | 70% | Targeted updates |

### Performance Metrics

**Before Optimization:**
- Average tokens per invocation: 3,500-5,000
- Documentation discovery: 2,000-3,000 tokens (Read all files)
- Full update: 5,000-8,000 tokens
- Cache utilization: 0%

**After Optimization:**
- Average tokens per invocation: 1,200-2,000
- Documentation discovery: 100-200 tokens (Glob only)
- Smart update: 1,200-2,000 tokens (git diff driven)
- Cache utilization: 80% (cache hits save 95%)
- **Overall reduction: 60%**

### Integration with Related Skills

**Synergy with other optimized skills:**
- `/understand` cache → Reuse architecture analysis (saves 2,000-4,000 tokens)
- `/changelog-auto` → Share version history cache (saves 500-1,000 tokens)
- `/readme-generate` → Share project metadata cache (saves 800-1,500 tokens)
- `/inline-docs` → Share framework detection (saves 600-1,200 tokens)
- `/commit` → Trigger docs update after commits (smart context)

---

**My approach:**
1. **Analyze our entire conversation** - Understand the full scope of changes
2. **Read ALL documentation files** - README, CHANGELOG, docs/*, guides, everything
3. **Identify what changed** - Features, architecture, bugs, performance, security, etc
4. **Update EVERYTHING affected** - Not just one file, but all relevant documentation
5. **Maintain consistency** - Ensure all docs tell the same story

**I won't make assumptions** - I'll look at what ACTUALLY changed and update accordingly.
If you refactored the entire architecture, I'll update architecture docs, README, migration guides, API docs, and anything else affected.

## Mode 1: Documentation Overview (Default)

When you run `/docs` without context, I'll:
- **Glob** all markdown files (README, CHANGELOG, docs/*)
- **Read** each documentation file
- **Analyze** documentation coverage
- **Present** organized summary

Output format:
```
DOCUMENTATION OVERVIEW
├── README.md - [status: current/outdated]
├── CHANGELOG.md - [last updated: date]
├── CONTRIBUTING.md - [completeness: 85%]
├── docs/
│   ├── API.md - [status]
│   └── architecture.md - [status]
└── Total coverage: X%

KEY FINDINGS
- Missing: Setup instructions
- Outdated: API endpoints (3 new ones)
- Incomplete: Testing guide
```

## Mode 2: Smart Update

When you run `/docs update` or after implementations, I'll:

1. **Run `/understand`** to analyze current codebase
2. **Compare** code reality vs documentation
3. **Identify** what needs updating:
   - New features not documented
   - Changed APIs or interfaces
   - Removed features still in docs
   - New configuration options
   - Updated dependencies

4. **Update systematically:**
   - README.md with new features/changes
   - CHANGELOG.md with version entries
   - API docs with new endpoints
   - Configuration docs with new options
   - Migration guides if breaking changes

## Mode 3: Session Documentation

When run after a long coding session, I'll:
- **Analyze conversation history**
- **List all changes made**
- **Group by feature/fix/enhancement**
- **Update appropriate docs**

Updates will follow your project's documentation style and conventions, organizing changes by type (Added, Fixed, Changed, etc.) in the appropriate sections.

## Mode 4: Context-Aware Updates

Based on what happened in session:
- **After new feature**: Update README features, add to CHANGELOG
- **After bug fixes**: Document in CHANGELOG, update troubleshooting
- **After refactoring**: Update architecture docs, migration guide
- **After security fixes**: Update security policy, CHANGELOG
- **After performance improvements**: Update benchmarks, CHANGELOG

## Smart Documentation Rules

1. **Preserve custom content** - Never overwrite manual additions
2. **Match existing style** - Follow current doc formatting
3. **Semantic sections** - Add to correct sections
4. **Version awareness** - Respect semver in CHANGELOG
5. **Link updates** - Fix broken internal links

## Integration with Skills

Works seamlessly with:
- `/understand` - Get current architecture first
- `/contributing` - Update contribution guidelines
- `/test` - Document test coverage changes
- `/scaffold` - Add new component docs
- `/security-scan` - Update security documentation

## Documentation Rules

**ALWAYS:**
- Read existing docs completely before any update
- Find the exact section that needs updating
- Update in-place, never duplicate
- Preserve custom content and formatting
- Only create new docs if absolutely essential (README missing, etc)

**Preserve sections:**
```markdown
<!-- CUSTOM:START -->
User's manual content preserved
<!-- CUSTOM:END -->
```

**Smart CHANGELOG:**
- Groups changes by type
- Suggests version bump (major/minor/patch)
- Links to relevant PRs/issues
- Maintains chronological order

**Important**: I will NEVER:
- Delete existing documentation
- Overwrite custom sections
- Change documentation style drastically
- Add AI attribution markers
- Create unnecessary documentation

After analysis, I'll ask: "How should I proceed?"
- Update all outdated docs
- Focus on specific files
- Create missing documentation
- Generate migration guide
- Skip certain sections

## Additional Scenarios & Integrations

### When to Use /docs

Simply run `/docs` after any significant work:
- After `/understand` - Ensure docs match code reality
- After `/fix-todos` or bug fixes - Update all affected documentation
- After `/scaffold` or new features - Document what was added
- After `/security-scan` or `/review` - Document findings and decisions
- After major refactoring - Update architecture, migration guides, everything

**I'll figure out what needs updating based on what actually happened, not rigid rules.**

### Documentation Types
I can manage:
- **API Documentation** - Endpoints, parameters, responses
- **Database Schema** - Tables, relationships, migrations
- **Configuration** - Environment variables, settings
- **Deployment** - Setup, requirements, procedures
- **Troubleshooting** - Common issues and solutions
- **Performance** - Benchmarks, optimization guides
- **Security** - Policies, best practices, incident response

### Smart Features
- **Version Detection** - Auto-increment version numbers
- **Breaking Change Alert** - Warn when docs need migration guide
- **Cross-Reference** - Update links between docs
- **Example Generation** - Create usage examples from tests
- **Diagram Updates** - Update architecture diagrams (text-based)
- **Dependency Tracking** - Document external service requirements

### Team Collaboration
- **PR Documentation** - Generate docs for pull requests
- **Release Notes** - Create from CHANGELOG for releases
- **Onboarding Docs** - Generate from project analysis
- **Handoff Documentation** - Create when changing teams
- **Knowledge Transfer** - Document before leaving project

### Quality Checks
- **Doc Coverage** - Report undocumented features
- **Freshness Check** - Flag stale documentation
- **Consistency** - Ensure uniform style across docs
- **Completeness** - Verify all sections present
- **Accuracy** - Compare docs vs actual implementation

### Smart Skill Combinations

**After analyzing code:**
```bash
/understand && /docs
# Analyzes entire codebase, then updates docs to match reality
```

**After fixing technical debt:**
```bash
/fix-todos && /test && /docs
# Fixes TODOs, verifies everything works, documents changes
```

**After major refactoring:**
```bash
/fix-imports && /format && /docs
# Fixes imports, formats code, updates architecture docs
```

**Before creating PR:**
```bash
/review && /docs
# Reviews code, then ensures docs reflect any issues found
```

**After adding features:**
```bash
/scaffold component && /test && /docs
# Creates component, tests it, documents the new API
```

### Simple Usage

Just run `/docs` and I'll figure out what you need:
- Fresh project? I'll show what docs exist
- Just coded? I'll update the relevant docs
- Long session? I'll document everything
- Just fixed bugs? I'll update CHANGELOG

No need to remember arguments - I understand context!

This keeps your documentation as current as your code while supporting your entire development lifecycle.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/manastalukdar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
