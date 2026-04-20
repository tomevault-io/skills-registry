---
name: doc-index
description: description: Use when adding, renaming, or reorganizing documentation files. Keep README.md and CLAUDE.md documentation indexes synchronized with docs/ directory. Use when this capability is needed.
metadata:
  author: maxlogvn
---
---
name: doc-index
description: Use when adding, renaming, or reorganizing documentation files. Keep README.md and CLAUDE.md documentation indexes synchronized with docs/ directory.
---

# Documentation Index

## Overview

Cập nhật Documentation Index trong `README.md` và `CLAUDE.md` khi docs/ thay đổi. **KHÔNG hardcode file names** — luôn scan và sync dynamically.

## Core Principle

> **Documentation Index = Source of Truth**
>
> Indexes phải phản ánh **thực tế** trong `docs/` directory, không phải danh sách files tĩnh. Mỗi khi thêm/xóa/di chuyển files → cập nhật indexes ngay.

## When to Use

```
✅ Tạo file .md mới trong docs/        → Update indexes
✅ Di chuyển/rename file docs          → Update references
✅ Tổ chức lại docs/ structure         → Reorganize indexes
✅ Xóa file documentation              → Remove from indexes
```

## Dynamic Workflow

### Step 1: Scan docs/ reality

```bash
# Liệt kê tất cả .md files hiện có
find docs -name "*.md" -type f | sort
```

**Expected output pattern**:
```
docs/CODING_STANDARDS.md
docs/DEPLOYMENT.md
docs/GETTING_STARTED.md
docs/SETUP.md
docs/TECH_STACK.md
docs/USERS.md
```

### Step 2: Detect missing files in indexes

```bash
# Check CLAUDE.md index
grep -E "\`docs/.*\.md\`" CLAUDE.md | sed 's/.*`\(.*\)`.*/\1/' | sort > /tmp/claude_docs.txt
find docs -name "*.md" -type f | sort > /tmp/actual_docs.txt
diff /tmp/claude_docs.txt /tmp/actual_docs.txt
```

**Output interpretation**:
- Lines starting with `<` → File có trong index nhưng KHÔNG có trong docs/ (xóa khỏi index)
- Lines starting with `>` → File có trong docs/ nhưng THIẾU trong index (thêm vào index)

### Step 3: Categorize files dynamically

**Classification by filename pattern**:

| Pattern | Category | Section (README) | Priority |
|---------|----------|------------------|----------|
| `SETUP.md` | Getting Started | Bắt đầu | 1 |
| `GETTING_STARTED.md` | Getting Started | Bắt đầu | 2 |
| `TECH_STACK.md` | Getting Started | Bắt đầu | 3 |
| `PROJECT_STRUCTURE.md` | Getting Started | Bắt đầu | 4 |
| `CODING_STANDARDS.md` | Development | Phát triển | 1 |
| `DESIGN_ADN.md` | Development | Phát triển | 2 |
| `*.md` (other core) | Development | Phát triển | 3 |
| `modules/*.md` | Operations | Operations | 1 |
| `DEPLOYMENT.md` | Operations | Operations | 2 |
| `FAQ.md` | Operations | Operations | 3 |

**Algorithm**:
1. Scan tất cả files trong `docs/`
2. Classify theo pattern và location (core vs modules/)
3. Sắp xếp theo priority trong mỗi category
4. Generate entries cho CLAUDE.md và README.md

### Step 4: Update CLAUDE.md Documentation Index

**Format**:
```markdown
## Documentation Index (Tra cứu nhanh)

| Chủ đề                     | Vị trí                      |
| -------------------------- | --------------------------- |
| [Generated dynamically]    | `docs/FILE.md`              |
```

**Link text generation rules**:
- `SETUP.md` → "Hướng dẫn khởi động"
- `GETTING_STARTED.md` → "Hướng dẫn chi tiết"
- `CODING_STANDARDS.md` → "Chuẩn code"
- `PROJECT_STRUCTURE.md` → "Cấu trúc dự án"
- `DESIGN_ADN.md` → "Design system / tokens"
- `TECH_STACK.md` → "Công nghệ sử dụng"
- `DEPLOYMENT.md` → "Triển khai"
- `FAQ.md` → "FAQ"
- `modules/*.md` → Tên topic (VD: "Hệ thống người dùng & rank" cho AUTH.md)
- Other files → Tên file without extension, Title Case

### Step 5: Update README.md sections

**Section structure**:
```markdown
### Bắt đầu
- **[Link Text](docs/FILE.md)** - Short description

### Phát triển
- **[Link Text](docs/FILE.md)** - Short description

### Operations
- **[Link Text](docs/FILE.md)** - Short description
```

**Description generation** (infer from filename):
- `SETUP.md` → "Clone, permissions, verify environment"
- `GETTING_STARTED.md` → "Cài đặt chi tiết"
- `CODING_STANDARDS.md` → "Conventions, patterns"
- `DESIGN_ADN.md` → "Design tokens, Brutalist style"
- `TECH_STACK.md` → "Dependencies chính xác"
- `PROJECT_STRUCTURE.md` → "Tổ chức thư mục"
- `DEPLOYMENT.md` → "Cấu hình production, vận hành"
- `FAQ.md` → "Troubleshooting"
- `modules/AUTH.md` → "Hệ thống phân quyền, test accounts"

### Step 6: Verify consistency

```bash
# 1. Count files in docs/
DOC_COUNT=$(find docs -name "*.md" -type f | wc -l)

# 2. Count entries in CLAUDE.md index
CLAUDE_COUNT=$(grep -c "\`docs/.*\.md\`" CLAUDE.md)

# 3. Count entries in README.md
README_COUNT=$(grep -c "\](docs/.*\.md)" README.md)

echo "Docs: $DOC_COUNT | CLAUDE.md: $CLAUDE_COUNT | README.md: $README_COUNT"
```

**Expected**: All counts should be equal (± reasonable margin for internal references)

### Step 7: Commit changes

```bash
git diff CLAUDE.md README.md  # Review
git add CLAUDE.md README.md
git commit -m "docs: sync documentation index với docs/ directory"
```

## Common Patterns

### Adding new documentation file

```bash
# 1. Create file
touch docs/NEW_TOPIC.md

# 2. Scan to detect
find docs -name "*.md" -type f | sort

# 3. Classify → Determine section
# If NEW_TOPIC.md → Core dev doc → "Phát triển" section

# 4. Update CLAUDE.md
| New Topic | `docs/NEW_TOPIC.md` |

# 5. Update README.md
### Phát triển
- **[New Topic](docs/NEW_TOPIC.md)** - Description

# 6. Verify & commit
```

### Moving file to modules/

```bash
# 1. Move file
git mv docs/USERS.md docs/modules/AUTH.md

# 2. Update ALL references
git grep -l "docs/USERS.md" | xargs sed -i 's|docs/USERS.md|docs/modules/AUTH.md|g'

# 3. Reclassify → Core → Module
# CLAUDE.md: Move entry from Development → Operations
# README.md: Move link from "Phát triển" → "Operations"

# 4. Verify no old paths remain
git grep "docs/USERS.md" .  # Should return nothing

# 5. Commit
git add -A && git commit -m "docs: move USERS.md → modules/AUTH.md"
```

### Deleting documentation

```bash
# 1. Remove file
git rm docs/OBSOLETE.md

# 2. Remove from CLAUDE.md index
# Delete the row for OBSOLETE.md

# 3. Remove from README.md
# Delete the link for OBSOLETE.md

# 4. Verify no dangling references
git grep "OBSOLETE" CLAUDE.md README.md  # Should return nothing

# 5. Commit
git commit -m "docs: remove obsolete OBSOLETE.md"
```

## Anti-Patterns

### ❌ Hardcoded file lists

```markdown
## Files to include:
- SETUP.md
- GETTING_STARTED.md
- CODING_STANDARDS.md
```

**Why wrong**: List becomes stale immediately after any change.

### ✅ Dynamic scanning

```markdown
## How to scan:
find docs -name "*.md" -type f | sort
```

**Why right**: Always reflects current state.

### ❌ Manual categorization

```
If file is X → put here
If file is Y → put there
```

**Why wrong**: Doesn't scale, requires updates for each new file.

### ✅ Pattern-based classification

```
If filename matches `SETUP.md` pattern → Getting Started (priority 1)
If file in `modules/` directory → Operations
```

**Why right**: Works for any file, scalable.

## Verification Checklist

Before committing:

- [ ] Ran `find docs -name "*.md"` to scan actual files
- [ ] All files in docs/ have corresponding entry in CLAUDE.md index
- [ ] All files in docs/ have corresponding link in README.md
- [ ] No references to deleted/moved files remain
- [ ] Link text is consistent between CLAUDE.md and README.md
- [ ] Files are in correct sections (Getting Started / Development / Operations)
- [ ] Table alignment correct in CLAUDE.md
- [ ] Tested: `grep` counts match actual file count

## Quick Reference Commands

```bash
# Scan docs
find docs -name "*.md" -type f | sort

# Detect missing in CLAUDE.md
grep -o "\`docs/[^)]*\.md\`" CLAUDE.md | sort -u

# Detect missing in README.md
grep -o "\](docs/[^)]*\.md)" README.md | sed 's/](//' | tr -d ')' | sort -u

# Verify all docs are indexed
find docs -name "*.md" -type f | while read f; do
  grep -q "$f" CLAUDE.md || echo "MISSING in CLAUDE.md: $f"
  grep -q "$f" README.md || echo "MISSING in README.md: $f"
done

# Count check
echo "Docs: $(find docs -name "*.md" | wc -l) | CLAUDE: $(grep -c '\`docs/' CLAUDE.md) | README: $(grep -c '](docs/' README.md)"
```

## Real-World Examples

### Case 1: Added MOCK_API.md

**What happened**:
```bash
# Created docs/MOCK_API.md
# Scan detected: 9 files (was 8)
# Classified: MOCK_API → Development (API-related)
# Added to CLAUDE.md index
# Added to README.md "Phát triển" section
# Commit: a639438
```

### Case 2: Moved USERS.md → modules/AUTH.md

**What happened**:
```bash
# git mv docs/USERS.md docs/modules/AUTH.md
# Reclassified: Core doc → Module doc
# Moved from "Phát triển" → "Operations" in README.md
# Updated all references with git grep
# Verified no old paths remain
# Commit: f40ce42
```

## Key Takeaway

**Documentation Index is a mirror, not a catalog.**

When `docs/` changes → Update indexes immediately.
Never maintain hardcoded lists.
Always scan dynamically.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxlogvn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
