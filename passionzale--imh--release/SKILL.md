---
name: release
description: | Use when this capability is needed.
metadata:
  author: PassionZale
---

## 发布流程

按照以下步骤执行发布流程，顺序不可跳过。

### 第一步：确定版本类型

从 `$ARGUMENTS` 读取 bump 类型。如果未提供或不确定，询问用户：
- `patch` — bug 修复、小调整 (x.y.z → x.y.z+1)
- `minor` — 新功能、功能增强 (x.y.z → x.y+1.0)
- `major` — 不兼容的变更 (x.y.z → x+1.0.0)

### 第二步：前置检查

依次检查以下条件，任一不满足则中止并告知用户原因：

1. **工作目录必须干净** — `git status --porcelain` 输出为空。否则提示用户先 commit 或 stash。
2. **当前分支必须有 upstream** — `git rev-parse --abbrev-ref --symbolic-full-name @{u}` 必须成功。
3. **目标 tag 不能已存在** — `git tag -l "v<NEW_VERSION>"` 输出为空。

### 第三步：更新 pubspec.yaml 版本号

从 `pubspec.yaml` 读取当前 `version:` 字段（去掉 `+build` 后缀）。

根据 bump 类型计算新版本号，然后用 Edit 工具更新 `pubspec.yaml`：
```
version: <NEW_VERSION>
```

### 第四步：生成 CHANGELOG 条目

分析上次 tag 至今的 git commit，生成 CHANGELOG 条目。

**获取 commit 列表：**
```bash
git log v<上一版本号>..HEAD --oneline --no-merges
```

如果没有新 commit，中止发布。

**将 commit 前缀映射到 CHANGELOG 分类**（只包含有条目的分类）：

| commit 前缀 | CHANGELOG 分类 |
|---|---|
| `feat` | Added |
| `fix` | Fixed |
| `refactor` | Changed |
| `perf` | Changed |
| `docs` | Changed |
| `breaking` / 前缀含 `!` | Removed |
| `security` | Security |

跳过 `chore:`、`ci:`、`test:` 和 merge commit。

**条目格式**：去掉 conventional commit 前缀（如 `feat: 新增深色模式` → `- 新增深色模式`）。保持简洁。

**写入 CHANGELOG**：在 `CHANGELOG.md` 的 `# Changelog` 标题之后插入新版本块，格式如下：

```markdown
## NEW_VERSION

### Added
- 条目 1
- 条目 2

### Fixed
- 条目 3
```

分类按此顺序排列：Added、Changed、Deprecated、Removed、Fixed、Security。只包含有条目的分类。

### 第五步：与用户确认

向用户展示发布摘要：
- 版本：`上一版本 → 新版本`
- 生成的 CHANGELOG 内容
- 当前分支名

询问："确认发布？确认后将 commit、打 tag 并 push。"

**重要**：必须等用户明确确认后才能继续。如果用户想修改 CHANGELOG 条目，等用户改完再继续。

### 第六步：提交、打 tag、推送

用户确认后依次执行：

1. **暂存文件：**
   ```bash
   git add pubspec.yaml CHANGELOG.md
   ```

2. **提交：**
   ```bash
   git commit -m "chore: bump version to vNEW_VERSION"
   ```

3. **创建 annotated tag：**
   ```bash
   git tag -a "vNEW_VERSION" -m "Release vNEW_VERSION"
   ```

4. **推送 commit 和 tag：**
   ```bash
   git push && git push --tags
   ```

5. **报告结果** — 告知用户版本已发布，GitHub Actions 将自动构建 APK。

---
> Source: [PassionZale/imh](https://github.com/PassionZale/imh) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
