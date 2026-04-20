---
name: git-commit
description: 遵循标准的 Git 提交规范，生成结构化、语义化的提交信息。使用约定式提交（Conventional Commits）格式，包含类型、范围、描述和详细说明。适用于任何需要提交代码变更的场景。 Use when this capability is needed.
metadata:
  author: liuhuo23
---

# Git 标准提交规范

遵循约定式提交（Conventional Commits）规范，生成清晰、一致、语义化的提交信息。
注意使用中文语言

## 使用时机

- 用户请求「提交代码」「commit」「生成提交信息」
- 完成功能开发、修复 bug 或重构代码后
- 需要创建符合团队规范的提交记录时
- 用户要求「标准提交」「规范提交」「按照约定式提交」
- 使用中文

## 提交信息格式

### 基本结构

```
<类型>[可选范围]: <描述>

[可选正文]

[可选脚注]
```

### 详细说明

#### 1. 类型（Type）

**必填**，用于说明提交的类别：

- **feat**: 新功能（feature）
- **fix**: 修复 bug
- **docs**: 文档变更
- **style**: 代码格式（不影响代码运行的变动，如空格、分号等）
- **refactor**: 重构（既不是新增功能，也不是修复 bug 的代码变动）
- **perf**: 性能优化
- **test**: 增加测试或修改测试代码
- **build**: 构建系统或外部依赖的变动（如 Cargo.toml、package.json）
- **ci**: CI 配置文件和脚本的变动
- **chore**: 其他不修改 src 或测试文件的变动
- **revert**: 回滚之前的提交

#### 2. 范围（Scope）

**可选**，用于说明提交影响的范围，如模块名、组件名等：

- `(editor)`: 编辑器核心
- `(view)`: 视图相关
- `(terminal)`: 终端处理
- `(rope)`: Rope 数据结构
- `(ui)`: 用户界面
- `(config)`: 配置相关
- `(deps)`: 依赖更新

#### 3. 描述（Description）

**必填**，简短描述本次变更：

- 使用祈使句，现在时："add" 而非 "added" 或 "adds"
- 不要大写首字母
- 结尾不加句号
- 限制在 50 个字符以内（中文约 25 字）
- 要清晰具体，避免模糊描述

**好的例子：**
- `add vim-style cursor movement`
- `fix cursor position after delete`
- `refactor rope implementation for better performance`

**不好的例子：**
- `Added some stuff` ❌ (过去时、模糊)
- `Fix bug.` ❌ (有句号)
- `Update code` ❌ (不具体)

#### 4. 正文（Body）

**可选**，提供更详细的变更说明：

- 空一行后开始
- 解释**为什么**做这个变更，而不仅是**做了什么**
- 可以分多段，每段说明一个方面
- 每行限制在 72 个字符以内

#### 5. 脚注（Footer）

**可选**，用于：

- **破坏性变更**：以 `BREAKING CHANGE:` 开头
- **关闭 issue**：`Closes #123` 或 `Fixes #456`
- **其他元数据**：如 `Reviewed-by:`, `Refs:`

## 提交工作流

### 1. 检查变更

```bash
git status
git diff
```

查看修改了哪些文件，确认变更内容。

### 2. 暂存文件

```bash
# 暂存所有变更
git add .

# 或选择性暂存
git add src/editor.rs src/view.rs
```

### 3. 生成提交信息

根据变更内容，按照上述格式生成提交信息。

### 4. 执行提交

```bash
# 直接提交（简单情况）
git commit -m "feat(editor): add line number display"

# 使用编辑器输入多行信息（推荐）
git commit
```

## 示例模板

### 示例 1：新功能

```
feat(editor): add syntax highlighting support

Implement basic syntax highlighting for Rust files using
regex-based token matching. This improves code readability
and provides better visual feedback for users.

- Add TokenType enum for different syntax elements
- Implement highlighting for keywords, strings, and comments
- Add configuration option to enable/disable highlighting

Closes #42
```

### 示例 2：Bug 修复

```
fix(view): correct cursor position after multi-byte character deletion

The cursor was incorrectly positioned when deleting multi-byte
UTF-8 characters, causing display glitches and potential crashes.

This fix ensures proper byte-to-char conversion using the rope
data structure's char_to_byte method.

Fixes #128
```

### 示例 3：重构

```
refactor(rope): optimize insert operation for better performance

Replace linear search with binary search in insert operation,
reducing time complexity from O(n) to O(log n) for large files.

Performance improvement: ~60% faster for files over 10,000 lines.
```

### 示例 4：文档更新

```
docs: add architecture diagram and quick reference

Add comprehensive documentation covering:
- System architecture overview
- Component interaction diagrams
- Quick reference for common operations
```

### 示例 5：破坏性变更

```
feat(api)!: change view initialization signature

BREAKING CHANGE: View::new() now requires a Terminal reference
as the first parameter instead of dimensions.

Migration guide:
- Old: `View::new(80, 24)`
- New: `View::new(&terminal)`

This change enables better terminal integration and automatic
dimension detection.
```

### 示例 6：依赖更新

```
build(deps): upgrade crossterm to 0.28.0

Update crossterm dependency to fix terminal compatibility issues
on macOS Sonoma.
```

## 最佳实践

### 1. 提交粒度

- **每次提交做一件事**：一个提交解决一个问题或实现一个功能
- **保持提交原子性**：每个提交都应该是可独立测试、可回滚的
- **避免混合类型**：不要在一个提交中既加功能又改格式

### 2. 提交时机

- **功能完整后提交**：确保提交的代码是可工作的
- **通过编译和测试**：提交前运行 `cargo check` 和 `cargo test`
- **及时提交**：不要积累太多变更，保持小步快走

### 3. 提交信息质量

- **使用英文**：对于开源项目或国际团队
- **保持一致性**：团队统一使用相同的规范
- **提供上下文**：让未来的自己和他人能理解为什么做这个变更

### 4. 提交前检查清单

- [ ] 代码通过编译：`cargo check` 
- [ ] 代码通过测试：`cargo test`
- [ ] 代码符合格式规范：`cargo fmt --check`
- [ ] 没有 lint 警告：`cargo clippy`
- [ ] 提交信息符合规范
- [ ] 提交内容原子化（只做一件事）

## 工具辅助

### commitizen

可以使用 commitizen 工具生成规范的提交信息：

```bash
# 安装
npm install -g commitizen cz-conventional-changelog

# 使用
git cz
```

### git hooks

使用 commitlint 在提交时自动检查提交信息格式：

```bash
# 安装
npm install --save-dev @commitlint/cli @commitlint/config-conventional

# 配置 .commitlintrc.json
{
  "extends": ["@commitlint/config-conventional"]
}
```

## 特殊场景

### 紧急修复（Hotfix）

```
fix!: critical security vulnerability in file parser

BREAKING CHANGE: File::parse() now returns Result instead of panicking.

This addresses CVE-2024-XXXXX by properly handling malformed input.
All callers must now handle potential errors.
```

### 合并提交

```
Merge branch 'feature/syntax-highlighting' into main

Implements syntax highlighting for Rust and Python files.
See merge request !15
```

### 回滚提交

```
revert: feat(editor): add line number display

This reverts commit a1b2c3d4.

The feature caused performance issues with large files.
Will be reimplemented with optimization in #156.
```

## 输出格式

在生成提交信息时，以代码块形式输出：

```
类型(范围): 描述

详细说明（如需要）

脚注（如需要）
```

然后提供命令：

```bash
git commit -m "类型(范围): 描述"
```

或对于多行提交信息：

```bash
git commit
# 然后在编辑器中粘贴完整的提交信息
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/liuhuo23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
