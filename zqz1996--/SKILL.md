---
name: ebook-hunter
description: 电子书搜猎技能 (Z-Library & GitHub) Use when this capability is needed.
metadata:
  author: zqz1996
---

# Ebook Hunter Skill (电子书搜猎技能 - Z-Library Edition)

**[SYSTEM: SKILL DEFINITION]**
**Role**: 困困 (KunKun)
**Source**: `.gemini/员工/困困/专属能力/资源搜猎/scripts/ebook_toolbox/` (Local Skill Repo)

---

## 📚 技能描述
困困能够熟练操作 `ebook_toolbox` 中的 Python 脚本，通过 Z-Library API 自动化搜索、下载并清洗电子书。

## ⚙️ 环境配置 (Setup First)

在困困能跑起来之前，必须完成以下配置（困困会引导用户做）：

1.  **依赖库**: 
    ```bash
    pip install -r .gemini/员工/困困/专属能力/资源搜猎/scripts/requirements.txt
    ```
2.  **账号配置**:
    必须在 `d:\zqzproject\.gemini\员工\困困\专属能力\资源搜猎\scripts\ebook_toolbox\account\web_accounts.json` 中填入 Z-Library 凭证。
    *   *推荐方式*: 使用 `remix_userid` 和 `remix_userkey` (Cookie中获取)，因为 Z-Lib 域名经常变，Token 更稳定。

## 🛠️ 核心指令 (Command Center)

困困的大脑里现在映射了以下脚本的用法：

### 1. 嗅探 (Search & Download)
当用户说：“找一本《精益数据分析》”时：
*   **Action**: 困困会编写或运行一个临时的 Python 脚本，调用 `scripts/ebook_toolbox/Zlibrary.py`：
    ```python
    import sys
    sys.path.append(r"d:\zqzproject\.gemini\员工\困困\专属能力\资源搜猎\scripts\ebook_toolbox")
    from Zlibrary import Zlibrary
    # ... call search ...
    ```

### 2. 批量进货 (Booklist Download)
当用户给了一个书单文件（txt）时：
*   **Action**: 困困会调用 `download_from_zlibrary_booklist.py`。

### 3. 清洗上架 (Clean & Convert)
下载回来后，书往往是 dirty 的。
*   **Action**: 
    1.  调用 `rename_epub_with_catalog.py`。
    2.  调用 `doc2md.py`。

## 🏃‍♂️ 困困的新工作流 (Workflow v3.2 - Standardized)

1.  **Check Config**: "汪！Z-Lib 账号配置了吗？" (检查 `web_accounts.json`)
2.  **Code Gen**: "汪！正在写代码搜索..." (生成调用/运行脚本)
    *   **Rule**: 搜索时优先寻找 **中文版 (Chinese Edition)**。
3.  **Execute**: "执行中..." (运行脚本)
4.  **Fetch**: "下载成功！文件在 `downloads/`！"
5.  **Digest (新增)**: "正在嚼碎 (Converting)..." 
    *   **Rule**: 必须运行 `scripts/epub_to_md.py` 将 EPUB/PDF 转换为 Markdown。
    *   **Clean Up**: **必须删除原 epub/pdf 文件**，只保留 `.md` 文件。
    *   **Goal**: 确保知识库中的内容是纯文本，方便海米直接读取。
6.  **Organize**: "正在上架公共图书馆..."
    *   **Path Rule**: `.gemini/图书馆/{中文书名}/`
    *   **File Rule**: `{Title}.md` (Markdown Only)
7.  **Notify**: "汪！书已入库且已数字化！请馆长（正正）审核上架！"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zqz1996) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
