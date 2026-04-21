---
name: doc-organizer
description: Apply Progressive Disclosure principles to organize large documentation projects. Restructure docs into hierarchical structure, reduce token usage by 95%+, and create README files for navigation. Use when this capability is needed.
metadata:
  author: tempuss
---

# Doc Organizer - Documentation Architecture Specialist

> **Purpose**: Transform chaotic documentation into a Progressive Disclosure structure, achieving 95%+ token reduction through systematic hierarchical organization.

## When to Use This Skill

Use this skill when the user's request involves:
- **Documentation chaos** - 300+ documents with no clear structure
- **Token optimization** - Reducing context window usage by 90%+
- **Progressive Disclosure** - Building Tier 1 (overview) → Tier 2 (category) → Tier 3 (details)
- **README generation** - Creating index files for navigation
- **File reorganization** - Moving files by purpose (not format)
- **Naming conventions** - Standardizing file/directory names

## Core Identity

You are a **documentation architect** who applies Progressive Disclosure principles to turn overwhelming documentation into navigable, token-efficient knowledge systems. You achieve 95%+ token savings through systematic hierarchical structuring.

---

## Core Principles (5 Rules)

### 1. Hierarchical Classification (3-4 Layers)

**Structure**:
```
project/
├── Layer 1: Top-level categories (by purpose)
│   ├── Layer 2: Sub-categories (by function)
│   │   └── Layer 3-4: Documents
```

**Example**:
```
_ecommerce/
├── for-customers/        # Layer 1: Purpose (customer-facing)
│   ├── security/         # Layer 2: Function
│   │   ├── README.md
│   │   └── trust-framework.md  # Layer 3: Document
```

---

### 2. Purpose-Based Organization (Not Format-Based)

**❌ Wrong (Format-Based)**:
```
project/
├── design/      # PPT, Figma
├── docs/        # All markdown
└── assets/      # Images
```

**✅ Right (Purpose-Based)**:
```
project/
├── _docs/           # Project hub
├── technical/       # System design
├── customer/        # Client-facing
├── compliance/      # Regulations
└── knowledges/      # Knowledge base
```

**Why**: Users search by need (e.g., "security docs"), not format (e.g., "all PDFs").

---

### 3. Consistent Naming Conventions

#### Directories
- **Plural, lowercase, hyphens**: `knowledges/`, `_docs/`, `meeting-notes/`
- **Prefix conventions**:
  - `_docs/` - Internal documentation hub
  - `for-*/` - External stakeholder folders (e.g., `for-pharma/`, `for-jmyoung/`)

#### Files (Technical Docs)
```
[number]-[topic]-[subtitle].md

Examples:
✅ 01-system-architecture.md
✅ 02-1-overview.md
✅ 04-2-table-definitions.md

❌ system_arch.md (abbreviation)
❌ SecurityOverview.md (camelCase)
```

#### Files (Business Docs)
```
[topic]-[category].md

Examples:
✅ executive-summary.md
✅ roi-analysis.md
✅ gtm-strategy.md
```

---

### 4. README Index Files (Required)

Every major directory must have a README.md with:

**Template**:
```markdown
# [Directory Name] Documentation Index

[1-2 sentence description]

---

## 📁 Directory Structure

\`\`\`
directory/
├── README.md
├── subdirectory1/
└── subdirectory2/
\`\`\`

## 📋 Document List

<!-- Template Example: Replace placeholder paths with your actual documentation files -->
| File | Topic | Key Content |
|------|-------|-------------|
| [file1.md](./file1.md) | Title | • Item 1<br>• Item 2 |

**Total: N documents**

## 🎯 Role-Based Recommendations

- **Executives**: doc1, doc2
- **Developers**: doc3, doc4

## 📚 Related Documents

- **Link1**: path - description
```

---

### 5. CLAUDE.md Master Index

**Location**: Project root
**Purpose**: Single entry point for entire documentation

**Key Sections**:
- **Project overview** (30 seconds)
- **Quick start** (1 minute)
- **Hierarchical navigation** structure
- **Role-based guides** (developer, PM, executive)
- **Token optimization** stats

---

## 5-Step Reorganization Process

### Step 1: Current State Analysis

```bash
# Understand current structure
tree -L 3

# Count documents
find . -name "*.md" | wc -l

# Identify patterns
find . -type d -maxdepth 2
```

**Questions**:
- Directory structure? (_docs/, technical/)
- Sub-directory criteria? (Function? Format?)
- Naming conventions?

---

### Step 2: Pattern Analysis

**Analyze existing patterns**:

| Pattern | Example | Assessment |
|---------|---------|------------|
| Format-based | `design/`, `docs/` | ❌ Replace with purpose |
| Purpose-based | `customer/`, `technical/` | ✅ Keep and expand |
| Mixed | Some OK, some not | ⚠️ Standardize |

---

### Step 3: Restructuring Plan

**Option A: Purpose-Based Reorganization** (Recommended)
- Move files by purpose (customer, technical, compliance)
- Create 3-4 top-level categories
- 3-7 sub-categories each

**Option B: In-Place Structuring**
- Keep directories, add structure
- Add README files
- Standardize naming

**Present 2 options to user before executing.**

---

### Step 4: File Reorganization

```bash
# Create new structure
mkdir -p customer/research customer/strategy
mv customer/*.md customer/research/
mv design/ai-*.md knowledges/analysis/

# Rename files
mv systemArch.md 01-system-architecture.md
```

**Principles**:
- Batch similar files
- Preserve git history (use `git mv` if possible)
- Move, don't copy (avoid duplicates)

---

### Step 5: Index Creation

**Tasks**:
1. Create README.md in each directory (use template above)
2. Create/update CLAUDE.md in project root
3. Generate hierarchical navigation structure

---

## Visualization Tools

### tree Command

```bash
# Full structure (depth limited)
tree -L 2

# With statistics
tree -L 2 --du --dirsfirst

# Exclude unnecessary files
tree -I "node_modules|.git|__pycache__"

# Save to file
tree -L 3 > tree.md
```

### find Command

```bash
# .md files only
find . -name "*.md"

# Directories only
find . -type d -maxdepth 2

# File count
find . -name "*.md" | wc -l
```

---

## Progressive Disclosure - Token Optimization

### Problem
- **Before**: 311 docs × 5KB = 1.5MB context
- **Token usage**: 20MB+ (exceeds limits)

### Solution: 3-Tier Hierarchical Structure

**Tier 1: Master Index** (5KB)
- Project overview
- Main categories
- Total documents

**Tier 2: Category Index** (15KB)
- Category details
- Sub-categories
- Document lists

**Tier 3: Document Index** (30KB)
- Individual documents
- Detailed navigation
- Cross-references

**Result**: 95%+ token savings

---

## Workflow Example

### Scenario: "Organize _ecommerce/ directory (311 docs)"

**Step 1: Analyze**
```bash
tree -L 2 _ecommerce/
find _ecommerce/ -name "*.md" | wc -l  # 311
```

**Output**:
```
_ecommerce/
├── 97 files (mixed purposes)
├── customer/
├── technical/
└── research/
```

**Assessment**:
- ✅ Purpose-based directories exist
- ❌ 97 files at root (no organization)
- ❌ No README files
- ❌ Inconsistent naming

---

**Step 2: Plan**

**Option A: Full Reorganization**
```
_ecommerce/
├── _docs/               # Hub
├── for-customers/       # Customer-facing (70 docs)
├── for-partners/        # Partnership (25 docs)
├── technical/           # Architecture (30 docs)
├── compliance/          # PCI-DSS (31 docs)
├── research/            # Market research (9 docs)
└── knowledges/          # Knowledge base (17 docs)
```

**Option B: In-Place Structure**
```
_ecommerce/
├── README.md (new)
├── customer/
│   ├── README.md (new)
│   └── [existing files]
├── technical/
│   ├── README.md (new)
│   └── [existing files]
```

**Present to user**: "I found 311 docs. Option A reorganizes into 7 purpose-based folders. Option B adds structure without moving files. Which do you prefer?"

---

**Step 3: Execute (Option A chosen)**

```bash
# Create structure
mkdir -p _ecommerce/{for-customers/{security,frameworks},for-partners,technical,compliance,research,knowledges}

# Move files by purpose
mv _ecommerce/*security*.md _ecommerce/for-customers/security/
mv _ecommerce/*proposal*.md _ecommerce/for-partners/
mv _ecommerce/*architecture*.md _ecommerce/technical/
```

---

**Step 4: Generate READMEs**

**_ecommerce/README.md**:
```markdown
# E-commerce Platform Documentation Index

E-commerce Platform Project - 311 documents

---

## 📁 Directory Structure

\`\`\`
_ecommerce/
├── for-customers/   # Customer support (70 docs)
├── for-partners/    # Partner proposals (25 docs)
├── technical/       # Technical design (30 docs)
└── compliance/      # Compliance documentation (31 docs)
\`\`\`

## 🎯 Role-Based Guide

- **Sales Team**: for-customers/ (customer persuasion)
- **Development Team**: technical/ (system design)
- **Executive Team**: for-partners/ (proposals)

## 📊 Statistics

- **Total**: 311 docs
- **Token savings**: 95%+ (hierarchical structure)
```

---

**Step 5: Create Hierarchical Navigation**

Document organization with clear categories and navigation structure.

---

**Result**:
- ✅ 311 docs organized into 7 purpose-based folders
- ✅ README.md in each folder (8 files)
- ✅ Hierarchical navigation structure
- ✅ 95%+ token savings
- ✅ Clear navigation for all roles

---

## Token Optimization Strategy

| Approach | Token Usage | Savings |
|----------|-------------|---------|
| **Load all docs** | ~20MB+ | 0% (exceeds limits) |
| **Load by category** | ~500KB | 75% |
| **Hierarchical priority** | ~50KB | 95%+ ⭐ |

**Best Practice**: Always load hierarchical index first, then selectively load categories.

---

## Quality Checklist

### Structure Reorganization

- [ ] All directories have clear purpose
- [ ] Sub-directories logically classified
- [ ] File naming conventions consistent
- [ ] README.md exists in each folder
- [ ] CLAUDE.md updated

### README Quality

- [ ] Directory structure tree
- [ ] Document list table
- [ ] Role-based recommendations
- [ ] Related document links
- [ ] Statistics (count, size)

### Index Quality

- [ ] 3-Tier hierarchy (master → category → document)
- [ ] Purpose-based organization
- [ ] Role-based navigation
- [ ] Use-case mapping
- [ ] Token savings documented

---

## Common Mistakes to Avoid

### ❌ Format-Based Organization
```
docs/      # All markdown
images/    # All images
videos/    # All videos
```

**Why wrong**: Users search by purpose, not format.

---

### ❌ Deep Nesting (5+ levels)
```
project/A/B/C/D/E/file.md
```

**Why wrong**: Hard to navigate, unclear purpose.

**Fix**: Keep 3-4 levels max.

---

### ❌ No README Files
```
technical/
├── file1.md
├── file2.md
└── file3.md  # No README.md
```

**Why wrong**: No navigation, unclear purpose.

**Fix**: Add README.md with document list.

---

### ❌ Inconsistent Naming
```
technical/
├── 01-architecture.md
├── design_doc.md       # Mixed style
└── SecurityOverview.md # CamelCase
```

**Why wrong**: Hard to scan, unprofessional.

**Fix**: Standardize to `[number]-[topic]-[subtitle].md`.

---

## References

### External Resources
- [Progressive Disclosure](https://www.nngroup.com/articles/progressive-disclosure/) - UX principle
- [Information Architecture](https://www.usability.gov/what-and-why/information-architecture.html) - IA best practices

---

## Output Format

When reorganizing documentation, provide:

1. **Current State Analysis**
   - Directory tree
   - File count
   - Issues identified

2. **Reorganization Plan**
   - Option A (recommended)
   - Option B (alternative)
   - User approval

3. **Execution**
   - Commands executed
   - Files moved
   - New structure

4. **Index Creation**
   - README.md files
   - Hierarchical navigation
   - CLAUDE.md

5. **Result Summary**
   - Before/After comparison
   - Token savings
   - Navigation improvements

---

For detailed usage and examples, see related documentation files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tempuss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
