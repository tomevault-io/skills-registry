---
name: rapid-contributing-guide
description: Creates or optimizes contributing/贡献指南 docs for RapidAI-style projects. Use when writing or updating docs/contributing.md to match the structure and conventions of RapidOCRDocs contributing guide (前置要求、一至八步骤、约定式提交、流程小结).
metadata:
  author: rapidai
---

# Rapid 风格贡献指南文档

在为本仓库或同类 Rapid 项目编写、优化 `docs/contributing.md` 时，按以下结构与约定执行。

## 文档结构（必须保留）

1. **前置要求**：Python 版本、Git、GitHub 账号
2. **一、克隆源码**：主仓库 clone，可选说明网络受限时先 Fork
3. **二、配置环境**：虚拟环境（venv/conda）+ `pip install -r requirements.txt`、`pytest`、`pip install -e .`，可选推理依赖文档链接
4. **三、安装 pre-commit**：在**仓库根目录**执行 `pre-commit install`，说明提交前自动检查及 `pre-commit run --all-files`
5. **四、运行单元测试**：`pytest tests/ -v`（及单文件、覆盖率示例），强调“确认基线通过再改”
6. **五、复现问题 / 增加新功能**：含“反馈问题与建议”（Bug/Feature/文档）及“复现 Bug”“增加新功能”子步骤
7. **六、编写对应单元测试**：测试目录、命名 `test_*.py`、资源目录（如 `tests/test_files/`）、测试原则 + **一段适配当前项目的 pytest 示例代码**
8. **七、运行所有单元测试**：再次全量 `pytest tests/ -v`，提醒跳过用例的注意点
9. **八、准备提交**：8.1 Fork、8.2 推送到个人 Fork（remote、分支、commit、push）、8.3 提 PR
10. **流程小结**：用表格列出步骤 1～10 的简短说明
11. **其他说明**：代码风格（black/autoflake、pre-commit）、文档链接、Issues 链接；可选“文档本地预览”（如 MkDocs）

## 项目适配（替换占位）

- **仓库名与链接**：全文替换为当前项目（如 `RapidAI/RapidLayout`、仓库 URL、文档站 URL、Issues URL）
- **目录结构**：
  - 若项目**有** `python/` 子目录：保留“进入 python 目录”、在 `python/` 下跑 pytest、`git add python/`、commit 范围可写 `fix(python):`
  - 若项目**无** `python/` 子目录：在仓库根目录克隆、安装、跑测；`git add .`；commit 类型示例用 `fix:`、`feat:`（可不带范围）
- **包名与测试**：所有 `rapidocr`/`rapid_layout` 等包名、`tests/` 路径、示例中的 engine 类名（如 `RapidOCR`/`RapidLayout`）按当前项目替换
- **文档站安装/使用链接**：替换为当前项目的安装页、使用页

## 约定式提交（必须包含）

在「8.2 将代码提交到个人 Fork」下保留 Conventional Commits 说明与下表：

| 类型       | 说明                   |
|------------|------------------------|
| `feat`     | 新功能                 |
| `fix`      | Bug 修复               |
| `docs`     | 文档变更               |
| `style`    | 代码格式（不影响逻辑） |
| `refactor` | 重构                   |
| `test`     | 测试相关               |
| `chore`    | 构建/工具等            |

格式：`<类型>[可选范围]: <简短描述>`，并给 1～2 个示例。

## PR 说明要点（必须包含）

在 8.3 中明确：base 为 `RapidAI/当前仓库`、base 分支 `main`；head 为贡献者 fork 与分支；说明中建议包含：对应 Issue（`Fixes #123`）、修改原因与改动、验证方式（如“在 xx 目录执行 `pytest tests/ -v` 通过”）。

## 可选内容

- **与 GitHub 同步提示**：若主仓库有 CONTRIBUTING 文件且以主仓库为准，可在文首加 MkDocs `!!! tip` 框，说明“文档站与主仓库同步，以 GitHub 为准”
- **文档本地预览**：若项目用 MkDocs，在「其他说明」前增加“文档本地预览”小节，写 `mkdocs serve` 及访问地址

## 写作注意

- 所有代码块需可复制即用，路径、包名、仓库名已替换为当前项目
- 流程小结表格与正文一～八、八的 8.1～8.3 一一对应，无遗漏
- 语气保持简洁、一致，结尾可保留“再次感谢你的贡献！”

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rapidai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
