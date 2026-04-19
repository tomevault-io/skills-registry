---
name: add-template
description: Add new UI style template to the ui-style-react project. This skill should be used when users want to add a new style template with HTML/CSS code, create a new preview page, or register a template in the system. Triggers include "add template", "create new style", "add new template", or when users provide HTML code for a new UI style. Use when this capability is needed.
metadata:
  author: tonnywong1052
---

# Add Template

## Overview

This skill automates the complete workflow for adding new UI style templates to the ui-style-react project. It handles directory creation, file generation, and automatic registration in the preview system.

## When to Use

- When a user says "add new template" or "create style template"
- When a user provides HTML/CSS code that should become a new template
- When a user wants to add a new style to `/styles/preview/` pages
- When migrating templates from external sources

## Quick Start

To add a new template, collect the following information:

| Parameter | Required | Description | Example |
|-----------|----------|-------------|---------|
| `category` | ✅ | Style category | `visual`, `core`, `retro`, `interaction`, `layout` |
| `familyId` | ✅ | Family/group name | `grain`, `glassmorphism`, `minimalism` |
| `templateId` | ✅ | Unique template ID | `visual-grain-film-noir` |
| `htmlContent` | ✅ | Full HTML content | Complete `<!DOCTYPE html>` page |
| `cssContent` | ✅ | CSS styles | Corresponding stylesheet |
| `titleZh` | Optional | Chinese title | `胶片黑色风格` |
| `titleEn` | Optional | English title | `Film Noir Style` |
| `setAsDefault` | **✅ Default: true** | Set new template as default (first in list) | `true` / `false` |

### ⭐ Default Template Behavior / 默认模板行为

**By default, newly added templates are set as the default template (first in the list).**

新添加的模板**默认会被设置为默认模板**（列表中的第一个）。

- `setAsDefault: true` (默认) - 新模板显示在第一位，用户打开预览页面首先看到新模板
- `setAsDefault: false` - 新模板添加到列表末尾

## Workflow

### 🚨🚨🚨 CRITICAL RULES (READ FIRST!) 🚨🚨🚨
### 关键规则（必须首先阅读！）

**Rule 1: ALWAYS check existing templates BEFORE creating manifest.json**
- List existing template directories in `public/data/content/styles/{category}/{familyId}/`
- The number of templates in manifest.json MUST equal the number of directories

**Rule 2: NEVER overwrite existing templates in manifest.json**
- When adding to an existing family, READ the current manifest.json first
- ADD your new template to the EXISTING templates array
- Do NOT create a new templates array with only your template

**Rule 3: New templates are DEFAULT by default (first position)**
- Unless user specifies otherwise, place new template FIRST in the templates array
- First template = default template shown when user opens preview page

**Rule 4: Verify templatesCount after build**
- After running build script, check that `templatesCount` matches expected value
- If templatesCount is wrong, you likely forgot to include existing templates

---

### ⚠️ Pre-flight Checklist (MUST DO FIRST!)
### ⚠️ 预检清单（必须首先执行！）

Before starting the workflow, **ALWAYS check for existing templates** in the target family:

```bash
# 1. Check if family directory exists and list existing templates
ls -la public/data/content/styles/{category}/{familyId}/ 2>/dev/null || echo "New family - no existing templates"

# 2. Check existing manifest.json
cat src/data/styles/generated/{category}/{familyId}/manifest.json 2>/dev/null | grep -A 30 '"templates"' || echo "No existing manifest"

# 3. Count existing templates
EXISTING_COUNT=$(ls -d public/data/content/styles/{category}/{familyId}/*/ 2>/dev/null | wc -l | tr -d ' ')
echo "Existing templates: $EXISTING_COUNT"
echo "Expected after adding: $((EXISTING_COUNT + 1))"
```

**Record the expected templatesCount** and verify it after Step 8!

---

### Step 1: Validate Parameters

Verify all required parameters are provided:
- `category` must be one of: `core`, `visual`, `retro`, `interaction`, `layout`
- `familyId` should match existing family or be a new valid name
- `templateId` should follow format: `{category}-{familyId}-{variant}` (kebab-case)
- `htmlContent` should be a complete HTML document with `<!DOCTYPE html>`
- `cssContent` should contain valid CSS

### Step 2: Create Directory Structure

Create the template directory:
```
public/data/content/styles/{category}/{familyId}/{templateId}/
├── fullpage.html
└── fullpage.css
```

Execute:
```bash
mkdir -p public/data/content/styles/{category}/{familyId}/{templateId}
```

### Step 3: Write Template Files

Write `fullpage.html`:
- Ensure the HTML links to `fullpage.css` with: `<link rel="stylesheet" href="fullpage.css">`
- Remove any inline `<style>` tags, move content to CSS file
- Keep `<script>` tags for interactivity

Write `fullpage.css`:
- Extract all CSS from the original HTML
- Include any CSS variables and custom properties

---

### 🚨 JSX Template Format Requirements (CRITICAL!)
### 🚨 JSX 模板格式要求（关键！）

If the user provides **React JSX code** instead of HTML, you MUST follow these rules:

**Rule 1: Use `export default function` syntax (NOT arrow function)**
**规则 1：使用 `export default function` 语法（不要用箭头函数）**

The JSX compiler (`jsxCompiler.js`) uses regex to strip `export` statements. It ONLY matches:
```javascript
export default function ComponentName()
```

JSX 编译器使用正则表达式来处理 export 语句，它**只能匹配**上述格式！

```javascript
// ✅ CORRECT - 正确格式（编译器可以处理）
import React, { useState } from 'react';
import { Icon1, Icon2 } from 'lucide-react';

export default function MyComponent() {
  const [state, setState] = useState(null);

  return (
    <div>...</div>
  );
}
// ← 文件在这里结束，不需要额外的 export 语句！
```

```javascript
// ❌ WRONG - 错误格式（会导致 "Unexpected token 'export'" 错误！）
import React, { useState } from 'react';
import { Icon1, Icon2 } from 'lucide-react';

const MyComponent = () => {  // ← 箭头函数
  const [state, setState] = useState(null);

  return (
    <div>...</div>
  );
};

export default MyComponent;  // ← 这行不会被编译器移除，导致运行时错误！
```

**Rule 2: File naming for JSX templates**
**规则 2：JSX 模板文件命名**

```
public/data/content/styles/{category}/{familyId}/{templateId}/
├── fullpage.jsx    ← React/JSX component
└── fullpage.css    ← Base styles (can be minimal for Tailwind CSS)
```

**Rule 3: Supported imports (automatically provided by runtime)**
**规则 3：支持的 imports（运行时自动提供）**

The React runtime (`reactRuntime.js`) provides these globally:
- All React hooks: `useState`, `useEffect`, `useRef`, `useMemo`, etc.
- All Lucide React icons: `Zap`, `Star`, `Heart`, `ArrowRight`, etc.
- React APIs: `createElement`, `Fragment`, `memo`, `forwardRef`, etc.

You can import them normally - the compiler will strip the imports and use the runtime-provided versions.

**Rule 4: Component name extraction**
**规则 4：组件名称提取**

The compiler extracts the component name from `export default function ComponentName`. Make sure:
- Component name starts with uppercase letter
- Component name matches the expected preview component

---

### Step 4: Update previewIdMapping

Edit `src/utils/previewLoader.js`:

Find the `previewIdMapping` object (around line 107) and add:
```javascript
'{templateId}': {
  category: '{category}',
  familyId: '{familyId}',
  templateId: '{templateId}'
},
```

**Important**: Add the entry in alphabetical order within the appropriate section (organized by category comments).

### Step 5: Update styleTagsMapping

Edit `src/data/metadata/styleTagsMapping.js`:

Find the `styleEnhancements` object and add:
```javascript
'{templateId}': {
  primaryCategory: '{category}',
  categories: ['{category}'],
  tags: ['contemporary'],
  relatedStyles: []
},
```

### ⚠️ Pre-flight Checklist (MUST DO FIRST!)

**🚨 THIS IS CRITICAL - NEVER SKIP THIS STEP! This prevents the "only one template showing" bug!**

Before doing anything, **ALWAYS check for existing templates** in the target family. This is the #1 cause of the manifest.json mistake!

```bash
# 1. Check if family directory exists and list existing templates
echo "=== Checking existing templates in {category}/{familyId} ===" && \
ls -la public/data/content/styles/{category}/{familyId}/ 2>/dev/null || echo "✓ New family - no existing templates"

# 2. Check existing manifest.json
echo "=== Checking existing manifest.json ===" && \
cat src/data/styles/generated/{category}/{familyId}/manifest.json 2>/dev/null | grep -A 50 '\"templates\"' || echo "✓ No existing manifest"

# 3. Count existing templates (CRITICAL!)
echo "=== Template Count ===" && \
EXISTING_COUNT=$(ls -d public/data/content/styles/{category}/{familyId}/*/ 2>/dev/null | wc -l | tr -d ' ') && \
echo "Existing template directories: $EXISTING_COUNT" && \
echo "Expected templatesCount after adding: $((EXISTING_COUNT + 1))"
```

**⚠️ SAVE THE NUMBERS ABOVE!** Use them to verify after Step 8!

If existing templates found: **SAVE the existing manifest.json** - you will add to it, not replace it!

---

### Step 6: Check Existing Templates (⚠️ CRITICAL!)
### 步骤 6：检查现有模板（⚠️ 关键！）

**Before creating or modifying manifest.json, you MUST check for existing templates!**
**在创建或修改 manifest.json 之前，必须检查现有模板！**

#### 6.1 List existing template directories

```bash
ls -la public/data/content/styles/{category}/{familyId}/
```

This will show all existing template directories. For example:
```
core-flat-design/
core-flat-design-ecommerce-landing/
```

#### 6.2 Check existing manifest.json (if exists)

```bash
cat src/data/styles/generated/{category}/{familyId}/manifest.json 2>/dev/null || echo "No existing manifest"
```

If manifest.json exists, **read and preserve** the existing templates array!

#### 6.3 Count expected templates

**Formula**: `Expected templatesCount = Existing templates + 1 (new template)`

Record this number to verify after the build.

---

### Step 7: Create/Update Manifest File (⚠️ PRESERVE EXISTING!)
### 步骤 7：创建/更新 Manifest 文件（⚠️ 保留现有模板！）

**Path**: `src/data/styles/generated/{category}/{familyId}/manifest.json`

```bash
mkdir -p src/data/styles/generated/{category}/{familyId}
```

#### Scenario A: New Family (no existing manifest)

Create new manifest.json:

```json
{
  "id": "{category}-{familyId}",
  "category": "{category}",
  "family": {
    "name": {
      "zh-CN": "{Chinese Title}",
      "en-US": "{English Title}"
    },
    "description": {
      "zh-CN": "{Chinese description}",
      "en-US": "{English description}"
    },
    "tags": ["contemporary", "other-tags"],
    "relatedStyles": ["related-style-1", "related-style-2"]
  },
  "templates": [
    {
      "id": "{templateId}",
      "title": {
        "zh-CN": "{Chinese Template Title}",
        "en-US": "{English Template Title}"
      }
    }
  ]
}
```

#### Scenario B: Existing Family (🚨 ADD to existing templates array!)

```
╔════════════════════════════════════════════════════════════════════════════╗
║  🚨 CRITICAL: THIS IS WHERE THE BUG HAPPENS!                             ║
║                                                                            ║
║  You MUST:                                                                 ║
║  1. READ the EXISTING manifest.json file                                   ║
║  2. COPY all existing templates from the array                             ║
║  3. ADD your new template to the array (first or last position)            ║
║  4. NEVER create a new array with only your template!                      ║
║                                                                            ║
║  ❌ WRONG: "templates": [ { "id": "new-only" } ]                          ║
║     This deletes all existing templates!                                   ║
║                                                                            ║
║  ✅ RIGHT: "templates": [                                                 ║
║       { "id": "existing-1" },  ← Keep!                                    ║
║       { "id": "new-template" }, ← Add!                                    ║
║       { "id": "existing-2" }    ← Keep!                                   ║
║     ]                                                                      ║
║                                                                            ║
║  If you skip this, the preview page will ONLY show 1 template!            ║
║  用户将只看到 1 个模板！                                                    ║
╚════════════════════════════════════════════════════════════════════════════╝
```

**DO NOT OVERWRITE the templates array!** You must:

1. Read the existing manifest.json
2. **Keep ALL existing templates** in the array
3. **Add the new template** based on `setAsDefault` setting:
   - `setAsDefault: true` (默认) → 添加到数组**开头**（第一位）
   - `setAsDefault: false` → 添加到数组**末尾**

Example - WRONG ❌:
```json
"templates": [
  { "id": "new-template-only", "title": {...} }  // 🚫 Lost existing templates!
]
```

Example - CORRECT ✅ (setAsDefault: true - 默认行为):
```json
"templates": [
  { "id": "new-template", "title": {...} },         // ⭐ NEW - 第一位 = 默认模板
  { "id": "existing-template-1", "title": {...} },  // ✅ Keep existing
  { "id": "existing-template-2", "title": {...} }   // ✅ Keep existing
]
```

Example - CORRECT ✅ (setAsDefault: false):
```json
"templates": [
  { "id": "existing-template-1", "title": {...} },  // ✅ Keep existing (still default)
  { "id": "existing-template-2", "title": {...} },  // ✅ Keep existing
  { "id": "new-template", "title": {...} }          // ✅ Add new at end
]
```

**Important**: 
- The `family.name` should match the family's visual/design style name
- The `templates` array **MUST contain ALL templates** in this family (existing + new)
- Include meaningful tags and relatedStyles for better discoverability
- **Verify templatesCount after build matches the number of templates in the array!**

### Step 8: Rebuild Styles Index

Run the build script to regenerate the consolidated styles index:

```bash
node scripts/build-styles-index.js
```

This script:
- Reads all manifest.json files from `src/data/styles/generated/`
- Aggregates metadata into a single index
- Outputs to `public/data/styles-index.json`
- Updates `templatesCount` for each family
- Ensures the style card appears in the frontend style list

**Verification**:
- Check the output for: `✨ Build complete!`
- Verify success count includes your new family
- Check that `templatesCount` is > 0 for your template

### Step 9: Stage Files for Git (⚠️ IMPORTANT!)
### 步骤 9：将文件添加到 Git 暂存区（⚠️ 重要！）

**After creating all files, you MUST stage them for git commit!**
**创建所有文件后，必须将它们添加到 git 暂存区！**

This prevents CI failures due to missing files that exist locally but weren't committed.
这可以防止因本地存在但未提交的文件而导致的 CI 失败。

```bash
# Stage all new template files
git add "public/data/content/styles/{category}/{familyId}/{templateId}/"
git add "src/data/styles/generated/{category}/{familyId}/manifest.json"
git add "public/data/styles-index.json"

# If JSX template, also stage compiled JSX
git add "public/data/compiled-jsx/{category}-{familyId}-{templateId}*" 2>/dev/null || true

# If previewIdMapping was updated
git add "src/utils/previewLoader.js"

# If styleTagsMapping was updated
git add "src/data/metadata/styleTagsMapping.js"

# Verify staged files
git status
```

**⚠️ Common Mistake**: Creating files locally but forgetting to commit them causes CI test failures like:
- `Cannot find module '...'`
- `Missing content directories for template`

---

### Step 10: Verify and Report

After completing all steps, verify:

1. **Template files exist**:
   ```bash
   ls -la public/data/content/styles/{category}/{familyId}/{templateId}/
   ```

2. **previewIdMapping** contains the new entry in `src/utils/previewLoader.js`

3. **styleTagsMapping** contains the new entry in `src/data/metadata/styleTagsMapping.js`

4. **manifest.json exists and contains ALL templates**:
   ```bash
   cat src/data/styles/generated/{category}/{familyId}/manifest.json | grep -A 50 '"templates"'
   ```
   
5. **Build script ran successfully**: Check for `✨ Build complete!` message

6. **⚠️ CRITICAL: Verify templatesCount matches expected value**:
   ```bash
   # Check templatesCount in styles-index.json
   cat public/data/styles-index.json | python3 -c "import sys,json; d=json.load(sys.stdin); families=[f for c in d['categories'].values() for f in c.get('families',[]) if f['familyId']=='{familyId}']; print(f'templatesCount: {families[0][\"templatesCount\"]}' if families else 'Family not found')"
   ```
   
   **Expected**: `templatesCount` should equal the number of template directories in `public/data/content/styles/{category}/{familyId}/`
   
   **If templatesCount is wrong**: Go back to Step 7 and ensure manifest.json includes ALL templates!

### Quick Verification Commands (Run After Step 8!)
### 快速验证命令（步骤 8 之后运行！）

```bash
# 1. Count template directories (should match templatesCount)
echo "=== Template Directories ===" && ls -d public/data/content/styles/{category}/{familyId}/*/ 2>/dev/null | wc -l

# 2. Check manifest.json templates count
echo "=== Manifest Templates ===" && cat src/data/styles/generated/{category}/{familyId}/manifest.json | grep -c '"id":'

# 3. Check styles-index.json templatesCount
echo "=== Index templatesCount ===" && grep -A 3 '"familyId": "{familyId}"' public/data/styles-index.json | grep templatesCount

# All three numbers above should be EQUAL!
```

**If numbers don't match**: Go back to Step 7 and ensure manifest.json includes ALL templates!

---

Output success report:
```
✅ Template created successfully!

📁 Files created:
   - public/data/content/styles/{category}/{familyId}/{templateId}/fullpage.html
   - public/data/content/styles/{category}/{familyId}/{templateId}/fullpage.css
   - src/data/styles/generated/{category}/{familyId}/manifest.json

📝 Registrations updated:
   - src/utils/previewLoader.js (previewIdMapping)
   - src/data/metadata/styleTagsMapping.js (styleEnhancements)
   - public/data/styles-index.json (rebuilt with templatesCount)

⭐ Default Template: {Yes/No}
   {If Yes: "This template will be shown first when users open the preview page"}

🌐 Preview URL: http://localhost:1000/styles/preview/{templateId}

✨ Frontend Integration:
   ✅ Style card will appear in the styles list
   ✅ Template is registered and discoverable
   ✅ Related styles are linked
   ⭐ {If default: "Set as default template (shown first)"}

🔧 Git Status:
   ✅ Files staged for commit (verify with: git status)

⚠️ Next steps:
   1. Verify staged files: git status
   2. Commit changes: git commit -m "✨ feat: add {templateId} template"
   3. Push to remote: git push
   4. Restart dev server: npm run dev
   5. Visit the preview URL to verify
   6. Check the styles list page to see the new style card
   7. Update tags in styleTagsMapping.js for better discoverability
```

## Interactive Mode

When parameters are incomplete, ask users step by step:

1. **Category Selection**:
   "Which category should this template belong to?"
   - core (Core design systems)
   - visual (Visual effects and styles)
   - retro (Retro/vintage styles)
   - interaction (Interactive elements)
   - layout (Layout patterns)

2. **Family ID**:
   "What family/group should this template belong to? (e.g., grain, glassmorphism, minimalism)"

3. **Template ID**:
   "What should the unique template ID be? Suggested format: {category}-{familyId}-{variant}"

4. **⭐ Set as Default Template** (默认模板设置):
   "Should this template be the default (shown first when users open the preview page)?"
   - **Yes (Recommended)** - 设为默认，用户打开预览页首先看到此模板
   - No - 添加到列表末尾
   
   **Note**: By default, new templates are set as the default template (first in list).

4. **HTML Content**:
   "Please provide the complete HTML content for the template."

5. **CSS Content**:
   "Please provide the CSS styles. If CSS is inline in the HTML, I'll extract it automatically."

## Directory Structure Reference

```
ui-style-react/
├── public/
│   └── data/
│       └── content/
│           └── styles/
│               └── {category}/       # core, visual, retro, etc.
│                   └── {familyId}/   # grain, glassmorphism, etc.
│                       └── {templateId}/ # visual-grain-film-noir
│                           ├── fullpage.html
│                           └── fullpage.css
├── src/
│   ├── utils/
│   │   └── previewLoader.js          # Contains previewIdMapping
│   └── data/
│       └── metadata/
│           └── styleTagsMapping.js   # Contains styleEnhancements
```

## Available Categories

| Category | Description |
|----------|-------------|
| `core` | Core design systems (minimalism, material design, flat design) |
| `visual` | Visual effects (grain, glassmorphism, neon, gradients) |
| `retro` | Retro/vintage styles (vaporwave, art deco, 80s) |
| `interaction` | Interactive elements (mouse tracking, scroll effects) |
| `layout` | Layout patterns (bento grids, masonry) |

## Adding Second/Multiple Templates to Existing Family
## 向现有 Family 添加第二个/多个模板（关键！）

When adding a new template variant to an **existing family** (e.g., adding a second Vaporwave template), you must update the `manifest.json` file. **This is the most common mistake!**

当向**现有 family** 添加新模板变体时（例如：向 Vaporwave 添加第二个模板），必须更新 `manifest.json` 文件。**这是最常见的错误！**

### Why This Matters / 为什么重要

The frontend reads the template list from `manifest.json`, **NOT** from the JS template file's `previews` array. If you only update the JS file but forget to update `manifest.json`, the new template will NOT appear in the preview selector.

前端从 `manifest.json` 读取模板列表，**而不是**从 JS 模板文件的 `previews` 数组。如果你只更新了 JS 文件而忘记更新 `manifest.json`，新模板将不会显示在预览选择器中。

### Checklist for Adding Template to Existing Family
### 向现有 Family 添加模板的清单

1. ✅ Create new template JS file (e.g., `templates/visual/vaporwave/vaporwaveDreams.js`)
   创建新的模板 JS 文件

2. ✅ Update family's `index.js` to import and add to `previews` array
   更新 family 的 `index.js`，导入并添加到 `previews` 数组

3. ✅ Add entry to `previewIdMapping` in `src/utils/previewLoader.js`
   在 `previewIdMapping` 中添加条目

4. ✅ Create content directory: `public/data/content/styles/{category}/{familyId}/{templateId}/`
   创建内容目录（fullpage.html + fullpage.css）

5. **⚠️ CRITICAL: Update `manifest.json`** - Add new template to `templates` array:
   **⚠️ 关键：更新 `manifest.json`** - 添加新模板到 `templates` 数组：

   **File**: `src/data/styles/generated/{category}/{familyId}/manifest.json`

   ```json
   {
     "templates": [
       {
         "id": "existing-template-id",
         "title": { "zh-CN": "现有模板", "en-US": "Existing Template" }
       },
       {
         "id": "new-template-id",
         "title": { "zh-CN": "新模板", "en-US": "New Template" }
       }
     ]
   }
   ```

6. ✅ Run build script: `node scripts/build-styles-index.js`
   运行构建脚本

7. ✅ Verify `templatesCount` increased in `public/data/styles-index.json`
   验证 `templatesCount` 已增加

### Quick Verification / 快速验证

After adding a new template variant, check:
添加新模板变体后检查：

```bash
# Check manifest.json has multiple templates
# 检查 manifest.json 有多个模板
cat src/data/styles/generated/{category}/{familyId}/manifest.json | grep -A 20 '"templates"'

# Verify templatesCount in styles-index.json
# 验证 styles-index.json 中的 templatesCount
grep -A 5 '"familyId": "{familyId}"' public/data/styles-index.json | grep templatesCount
```

---

## Common Issues

### 🚨 CI Test Failure: "Cannot find module" or "Missing content directories" (NEW!)
### 🚨 CI 测试失败："Cannot find module" 或 "Missing content directories"（新！）

**Symptoms / 症状**:
- CI/GitHub Actions fails with: `Cannot find module '...'`
- CI fails with: `Missing content directories for template`
- Tests pass locally but fail in CI
- Files exist on your machine but CI can't find them

**Root Cause / 根本原因**:
You created files locally but **forgot to commit them to git**! Untracked files (shown as `??` in `git status`) won't be pushed to the remote repository.

你在本地创建了文件，但**忘记提交到 git**！未追踪的文件（在 `git status` 中显示为 `??`）不会被推送到远程仓库。

**Solution / 解决方案**:

```bash
# 1. Check for untracked files
git status

# 2. Stage the missing files
git add "public/data/content/styles/{category}/{familyId}/{templateId}/"
git add "src/data/styles/generated/{category}/{familyId}/manifest.json"
git add "public/data/compiled-jsx/*"  # If JSX template
git add "src/utils/*.js"              # If new utility files

# 3. Commit and push
git commit -m "🐛 fix: add missing template files"
git push
```

**Prevention / 预防**:
- Always run `git status` before pushing
- Follow Step 9 (Stage Files for Git) in this workflow
- The success report includes a Git Status section - verify it!

---

### 🚨 JSX Runtime Error: "Unexpected token 'export'" (NEW!)
### 🚨 JSX 运行时错误："Unexpected token 'export'"（新！）

**Symptoms / 症状**:
- Preview shows: `Runtime Error: Uncaught SyntaxError: Unexpected token 'export' (line XXX)`
- The line number matches the location of `export default ComponentName;` at the end of the file

**Root Cause / 根本原因**:
The JSX file uses **arrow function + separate export** syntax, which the JSX compiler cannot process:

```javascript
// ❌ This pattern causes the error:
const MyComponent = () => { ... };
export default MyComponent;  // ← This line is NOT stripped by compiler!
```

**Solution / 解决方案**:
Convert to `export default function` syntax:

```javascript
// ✅ Change to this:
export default function MyComponent() {
  // ... component code
}
// No separate export statement needed!
```

**Technical Explanation / 技术说明**:
The JSX compiler (`src/utils/jsxCompiler.js`) uses this regex to match exports:
```javascript
const exportMatch = processedCode.match(/export\s+default\s+function\s+(\w+)/);
processedCode = processedCode.replace(/export\s+default\s+function\s+/, 'function ');
```

It ONLY matches `export default function Name`, not `export default Name` (variable export).

---

### 🚨 CRITICAL BUG: Second/Multiple Templates Not Showing! (Most Common!)
### 🚨 关键 BUG：第二个/多个模板未显示！（最常见！）

**This happens when you add a new template to an existing family but manifest.json only shows 1 template!**

**这发生在你添加新模板到现有 family 时，但 manifest.json 只显示 1 个模板！**

**Symptoms / 症状**:
- Preview page shows "Only 1 template available"
- Dropdown/selector only has 1 template option
- Expected 2+ templates, but only 1 shows
- Build script shows `templatesCount: 1` instead of `2`

**Root Cause / 根本原因**:
You **replaced** the existing templates array instead of **adding** to it. 

Example of WRONG approach:
```json
// ❌ WRONG: This deletes the original template!
"templates": [
  { "id": "visual-antiDesign-studio", "title": {...} }  // Lost the original!
]
```

**Solution / 解决方案** (Do This NOW):

1. **Read the existing manifest.json** and check what templates exist:
   ```bash
   cat src/data/styles/generated/{category}/{familyId}/manifest.json | grep -A 50 '"templates"'
   ```

2. **Edit manifest.json** and **ADD your new template** to the existing array:
   ```json
   "templates": [
     { "id": "visual-tech-anti-design", "title": {...} },      // ← EXISTING
     { "id": "visual-antiDesign-studio", "title": {...} }      // ← NEW (added)
   ]
   ```

3. **Rebuild the styles index**:
   ```bash
   node scripts/build-styles-index.js
   ```

4. **Verify templatesCount increased**:
   ```bash
   # Before: templatesCount: 1
   # After: templatesCount: 2
   grep -A 3 '"familyId": "{familyId}"' public/data/styles-index.json | grep templatesCount
   ```

5. **Restart dev server**:
   ```bash
   npm run dev
   ```

6. **Check preview page** - Should now show both templates!

---

### ⚠️ Second template not showing in preview selector (Most Common!)
### ⚠️ 第二个模板没有显示在预览选择器中（最常见！）

**Cause / 原因**: `manifest.json` was not updated with the new template entry. The frontend reads template list from `manifest.json`, not from the JS file's `previews` array.

`manifest.json` 没有更新新模板条目。前端从 `manifest.json` 读取模板列表，而不是从 JS 文件的 `previews` 数组。

**Solution / 解决方案**:
1. Edit `src/data/styles/generated/{category}/{familyId}/manifest.json`
2. Add new template to the `templates` array
3. Run `node scripts/build-styles-index.js`
4. Verify `templatesCount` is now correct (e.g., 2 instead of 1)
5. Restart dev server

**Example / 示例**:
```json
// Before (错误 - 只有1个模板)
"templates": [
  { "id": "visual-vaporwave-vaporwave-aesthetic", "title": {...} }
]

// After (正确 - 2个模板)
"templates": [
  { "id": "visual-vaporwave-vaporwave-aesthetic", "title": {...} },
  { "id": "visual-vaporwave-vaporwave-dreams", "title": {...} }
]
```

### Template not showing in style list
**Cause**: `templatesCount` is 0 in `public/data/styles-index.json`

**Solution**:
1. Ensure manifest.json exists at: `src/data/styles/generated/{category}/{familyId}/manifest.json`
2. Manifest must be in correct JSON format with valid `templates` array
3. Run build script: `node scripts/build-styles-index.js`
4. Verify `templatesCount` is > 0 in `public/data/styles-index.json`

### Template not showing in preview (404 error)
**Cause**: Preview route mapping or file path mismatch

**Solutions**:
- Verify directory path matches exactly: `public/data/content/styles/{category}/{familyId}/{templateId}/`
- Check that `templateId` in previewIdMapping matches the directory name exactly
- Verify previewIdMapping entry is added correctly
- Restart the dev server after making changes

### CSS not loading in preview
**Cause**: Missing or incorrectly named CSS file

**Solutions**:
- Ensure `fullpage.html` has: `<link rel="stylesheet" href="fullpage.css">`
- Check that CSS file is named exactly `fullpage.css`
- Verify CSS file is in the correct directory: `public/data/content/styles/{category}/{familyId}/{templateId}/`

### Build script fails or shows ❌ for new family
**Cause**: manifest.json is missing or malformed

**Solutions**:
1. Verify manifest.json exists and is valid JSON
2. Check manifest structure matches the template in Step 6
3. Ensure `templates` array is not empty
4. Run build script again: `node scripts/build-styles-index.js`

### Template ID conflicts
- Use unique IDs following the pattern: `{category}-{familyId}-{variant}`
- Check existing mappings before creating new ones
- Verify no duplicate entries in previewIdMapping

## Automated Script (Recommended!)
## 自动化脚本（推荐！）

**IMPORTANT**: The project has a built-in `create-template.js` script that automates the entire workflow, including **manifest.json update**!

**重要**：项目有内置的 `create-template.js` 脚本，可以自动化整个工作流，包括**自动更新 manifest.json**！

### Quick Usage / 快速使用

```bash
# Interactive mode (recommended) / 交互式模式（推荐）
npm run create-template

# Or with CLI parameters / 或使用命令行参数
npm run create-template -- \
  -c visual \
  -f vaporwave \
  -t visual-vaporwave-dreams \
  --title-zh "梦幻美学" \
  --title-en "Dreams Aesthetic"
```

### What the Script Does Automatically / 脚本自动完成的工作

1. ✅ Creates directory structure: `public/data/content/styles/{category}/{family}/{templateId}/`
2. ✅ Generates HTML/CSS template files (demo.html, fullpage.html, etc.)
3. ✅ **Updates `manifest.json`** - Adds new template to the templates array
4. ✅ Updates `styleTagsMapping.js` - Adds template enhancement config
5. ✅ Creates Prompt files (optional): `custom.md` and `style.md`

### CLI Parameters / 命令行参数

| Parameter | Short | Description | Required |
|-----------|-------|-------------|----------|
| `--category` | `-c` | Category (core/visual/retro/interaction/layout) | ✅ |
| `--family` | `-f` | Family name | ✅ |
| `--template-id` | `-t` | Template ID (format: {category}-{family}-{variant}) | ✅ |
| `--title-zh` | - | Chinese title | ❌ |
| `--title-en` | - | English title | ❌ |
| `--format` | - | File format (html/jsx) | ❌ |
| `--skip-prompts` | - | Skip Prompt file creation | ❌ |

### Examples / 示例

```bash
# Add second template to existing vaporwave family
# 向现有 vaporwave family 添加第二个模板
npm run create-template -- \
  -c visual \
  -f vaporwave \
  -t visual-vaporwave-neon-city \
  --title-zh "霓虹都市" \
  --title-en "Neon City"

# JSX format template
# JSX 格式模板
npm run create-template -- \
  -c visual \
  -f glassmorphism \
  -t visual-glassmorphism-dashboard \
  --title-zh "仪表板" \
  --title-en "Dashboard" \
  --format jsx

# Skip Prompt files
# 跳过 Prompt 文件
npm run create-template -- \
  -c retro \
  -f arcadeCRT \
  -t retro-arcadeCRT-game-ui \
  --title-zh "游戏界面" \
  --title-en "Game UI" \
  --skip-prompts
```

### After Running the Script / 脚本运行后

1. **Edit template files** - Add your actual HTML/CSS content
   编辑模板文件 - 添加实际的 HTML/CSS 内容

2. **Run build script** - Regenerate styles index
   运行构建脚本 - 重新生成样式索引
   ```bash
   node scripts/build-styles-index.js
   ```

3. **Add to previewIdMapping** - If preview route is needed
   添加到 previewIdMapping - 如果需要预览路由
   ```javascript
   // src/utils/previewLoader.js
   '{templateId}': { category: '{category}', familyId: '{familyId}', templateId: '{templateId}' },
   ```

4. **Restart dev server and test**
   重启开发服务器并测试
   ```bash
   npm run dev
   ```

### Script Architecture / 脚本架构

```
scripts/
├── create-template.js          # Main CLI entry point
└── lib/
    ├── interactive.js          # Interactive input logic
    ├── generators.js           # File template generators
    ├── metadata-updater.js     # manifest.json & styleTagsMapping.js updater
    └── validator.js            # Input validators
```

---

## Resources

### references/
See `references/existing-families.md` for a complete list of existing family IDs and their descriptions.

### scripts/
- **`scripts/create-template.js`** - Main template creation script (recommended!)
- **`scripts/build-styles-index.js`** - Rebuild styles-index.json after changes
- **`scripts/README.md`** - Full documentation for the create-template script

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tonnywong1052) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
