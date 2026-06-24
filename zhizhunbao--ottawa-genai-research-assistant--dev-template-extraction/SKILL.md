---
name: template-extraction
description: Methodology for extracting reusable code templates from open-source reference projects. Use when (1) analyzing a reference project for extractable modules, (2) creating .template files from source code, (3) migrating modules into the project, (4) planning template extraction sprints. Keywords — "extract template", "create template", "reference project", "module extraction", "migrate module Use when this capability is needed.
metadata:
  author: zhizhunbao
---

# Template Extraction Methodology

## Core Principle

> **能直接用绝对不自己写** — If it exists in open-source, extract it as a template. Never write from scratch.

---

## 1. The 5-Level Thinking Framework

Different operations naturally map to different granularity levels. **Module** is the center of gravity:

```
Level 1: 项目 Project    → SELECT sources    (which project is worth studying?)
Level 2: 层   Layer       → CLASSIFY placement (backend / frontend / agent / devops)
Level 3: 模块 Module      → DEFINE boundaries  ★ CORE LEVEL ★
Level 4: 文件 File        → EXTRACT templates  (each .template file)
Level 5: 生成 Generate    → ADAPT to project   (AI generates final code from template)
```

### Why Module is the Best Granularity

| Level | Too Coarse? | Too Fine? | Module is Just Right |
|:---|:---|:---|:---|
| Project | Can't reuse directly — architecture differs | — | — |
| Layer | Each layer contains very different things | — | — |
| **Module** | — | — | ✅ Self-contained, clear boundaries, portable |
| File | — | Loses context, missing dependencies | — |

### Operation-to-Level Mapping

| Operation | Best Level | What It Means |
|:---|:---|:---|
| **参考 Reference** | 🏗️ Project | Study architecture decisions and design philosophy |
| **模板 Template** | 📄 File | Atomic unit of reuse — one file with `{{placeholders}}` |
| **迁移 Migrate** | 📦 Module | Move a cohesive feature unit (e.g., entire auth module) |
| **创建 Create** | 🧱 Layer | Build project skeleton layer by layer |
| **生成 Generate** | 📄 File | AI fills in file details using module context |

### The Dependency Chain

```
Reference(Project) → Template(File) → Migrate(Module) → Create(Layer) → Generate(File)
      ↓                    ↓                ↓                ↓               ↓
    Study              Distill           Transport        Scaffold        Fill details
```

---

## 2. Module Card — Standard Format

Every extractable module MUST be documented as a **Module Card**. This is the atomic unit of the template system.

### Module Card Template

```markdown
## 📦 Module: {{MODULE_NAME}}

**Source**: `{{REFERENCE_PROJECT}}/{{SOURCE_PATH}}/`
**Target**: `.agent/templates/{{TARGET_PATH}}/`
**Layer**: {{LAYER}} (backend / frontend / agent / orchestration / devops)
**Priority**: {{PRIORITY}} (🔴 Critical / 🟠 High / 🟡 Medium / 🔵 Low)

### Description
{{One paragraph describing what this module does and why it's worth extracting}}

### File Manifest

| # | Source File | Template Output | Role |
|---|------------|-----------------|------|
| 1 | `source/component.tsx` | `target/component.tsx.template` | Main component |
| 2 | `source/types.ts` | `target/types.ts.template` | Type definitions |
| 3 | `source/hook.ts` | `target/hook.ts.template` | Business logic |

### Dependencies
- **npm**: `react-markdown`, `@radix-ui/popover`
- **internal**: `shared/components/ui/popover`

### Adaptation Notes
- Replace `{{API_BASE_URL}}` with project API endpoint
- Replace `{{ALIAS}}` with project path alias (`@/`)
- i18n: Wrap user-facing strings with `t()`

### Quality Checklist
- [ ] All files have `@source` annotation in header
- [ ] All `{{placeholder}}` variables documented
- [ ] Dependencies listed in package.json / requirements.txt
- [ ] Usage example provided
- [ ] Registered in `docs/templates/README.md`
```

---

## 3. Extraction Workflow (Step-by-Step)

### Phase 1: Project-Level Analysis (参考)

```
Input:  A reference project in `.github/references/`
Output: List of extractable modules with priority ranking
```

**Steps:**

1. **Scan project structure** — `list_dir` the `src/` or `app/` directory
2. **Identify high-value modules** — Look for:
   - Self-contained feature directories
   - Reusable utility libraries
   - Patterns not yet in our template system
3. **Score each module** using the Value Matrix:

| Factor | Weight | Question |
|:---|:---|:---|
| Reusability | 30% | Can this be used in 3+ projects? |
| Complexity | 25% | Would this take >2h to write from scratch? |
| Quality | 25% | Is this production-grade code? |
| Relevance | 20% | Does our project need this? |

4. **Output**: Module Cards for top-ranked modules

### Phase 2: Module-Level Extraction (模板)

```
Input:  A Module Card
Output: .template files in `.agent/templates/`
```

**Steps:**

1. **Read all source files** in the module
2. **Identify hardcoded values** → Replace with `{{PLACEHOLDER}}`
3. **Remove project-specific logic** → Keep only generic pattern
4. **Add file header annotations**:

For Python:
```python
"""
{{ModuleName}} - {{Description}}

@module {{module_path}}
@source {{reference_project}}/{{source_file_path}}
@reference {{github_url}}
@template {{template_id}}
"""
```

For TypeScript/TSX:
```tsx
/**
 * {{ComponentName}} - {{Description}}
 *
 * @module {{module_path}}
 * @source {{reference_project}}/{{source_file_path}}
 * @reference {{github_url}}
 * @template {{template_id}}
 */
```

5. **Write `.template` file** to `.agent/templates/{{target_path}}/`
6. **Update documentation** in `docs/templates/0X-*.md`

### Phase 3: Module-Level Migration (迁移)

```
Input:  .template files for a module
Output: Working code in the project
```

**Steps:**

1. **Copy all template files** in the module to target directory
2. **Remove `.template` extension**
3. **Replace all `{{PLACEHOLDER}}` values** with project-specific values
4. **Install dependencies** (`npm install` / `pip install`)
5. **Update imports** — Adjust path aliases
6. **Add i18n** — Wrap user-facing strings
7. **Update `@template` tag** in each file to point to source template
8. **Verify build** — Run `npm run build` / `pytest`

### Phase 4: Layer-Level Creation (创建)

```
Input:  A new layer or feature to build
Output: Complete feature skeleton
```

**Steps:**

1. **Identify which modules** are needed for this layer
2. **Migrate modules** in dependency order (types → hooks → components → views)
3. **Wire up routing** — Add to `app.tsx` or `main.py`
4. **Connect to existing infrastructure** — Auth, API client, stores

### Phase 5: File-Level Generation (生成)

```
Input:  A template file + project context
Output: Final adapted code file
```

**Steps:**

1. **Read the template** and understand its pattern
2. **Analyze project context** — Existing types, API endpoints, store shape
3. **Generate adapted code** — Fill in all placeholders, adjust types, add tests
4. **Add proper file header** with `@template` and `@reference` tags

---

## 4. Standard Placeholders

Use these standardized placeholder names consistently across all templates:

| Placeholder | Description | Example |
|:---|:---|:---|
| `{{ModuleName}}` | PascalCase module name | `ChatCitation` |
| `{{module_name}}` | snake_case module name | `chat_citation` |
| `{{moduleName}}` | camelCase module name | `chatCitation` |
| `{{FeatureName}}` | PascalCase feature name | `Document` |
| `{{feature_name}}` | snake_case feature name | `document` |
| `{{ALIAS}}` | Import path alias | `@` or `@/` |
| `{{API_BASE_URL}}` | API base URL | `/api/v1` |
| `{{APP_NAME}}` | Application name | `Ottawa GenAI` |
| `{{TABLE_NAME}}` | Database table name | `chat_sessions` |
| `{{ROUTE_PREFIX}}` | API route prefix | `/chat` |

---

## 5. Template File Naming Convention

```
.agent/templates/
├── {layer}/                          # Layer directory
│   ├── {module}/                     # Module directory (if multi-file)
│   │   ├── component.tsx.template    # Individual template file
│   │   ├── types.ts.template
│   │   └── hook.ts.template
│   └── single-file.py.template       # Single-file module
```

**Rules:**
- Extension: always `.template` appended to the real extension
- Naming: match the target filename (e.g., `chat-input.tsx.template`)
- Organization: mirror the project's directory structure

---

## 6. Documentation Registry

Every template must be registered in two places:

### 6.1 Detail Doc (`docs/templates/0X-*.md`)

Each layer has its own detail document:
- `01-backend-templates.md`
- `02-frontend-templates.md`
- `03-ai-agent-templates.md`
- etc.

The detail doc contains:
- Full code snippets for each template
- Source file links
- Key features and usage notes

### 6.2 Master Index (`docs/templates/README.md`)

The master index contains:
- Directory tree overview
- Source traceability table (template → reference project)
- Status summary (extracted count / pending count)

---

## 7. Decision Tree: When to Extract vs. Skip

```
Is this module available in a reference project?
├── YES → Is it self-contained (< 10 files, clear boundaries)?
│   ├── YES → Is it generic enough to reuse in 3+ projects?
│   │   ├── YES → ✅ EXTRACT as template
│   │   └── NO  → 🔧 EXTRACT but mark as "project-specific adaptation needed"
│   └── NO  → Is the valuable part isolatable?
│       ├── YES → ✅ EXTRACT the valuable subset
│       └── NO  → ⏸️ SKIP — use as reference only, document patterns in ARCHITECTURE_ANALYSIS.md
└── NO → Is there a well-known npm/pip package?
    ├── YES → 📦 USE the package directly (e.g., `npx shadcn add`)
    └── NO  → ✍️ WRITE from scratch (last resort)
```

---

## 8. Quick Reference: Module Extraction Checklist

When extracting a module, verify each step:

- [ ] **Module Card** created with all fields
- [ ] **Source files** read and understood
- [ ] **Placeholders** identified and replaced with `{{STANDARD_NAME}}`
- [ ] **Project-specific logic** removed or flagged
- [ ] **File headers** added with `@source`, `@template`, `@reference`
- [ ] **Dependencies** documented (npm/pip packages)
- [ ] **Adaptation notes** written (what to change when using)
- [ ] **Template files** written to `.agent/templates/`
- [ ] **Detail doc** updated (`docs/templates/0X-*.md`)
- [ ] **Master index** updated (`docs/templates/README.md`)
- [ ] **Usage example** provided in template file or docs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zhizhunbao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
