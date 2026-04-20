---
name: git-worktree
description: 該 skill 用於建立 git worktree Use when this capability is needed.
metadata:
  author: z0890142
---

# git-worktree

## 規則

1. 使用以下 Bash 取得當前 branch
```bash
    BASE_BRANCH=$(git branch --show-current)
```

2. worktree 命名規則
```
wt-${openspec proposal name}-{date}
```

3. 透過以下方式建立 worktree
```bash
BASE_BRANCH=$(git branch --show-current)
WT_BRANCH=wt-${openspec proposal name}-{date}

git worktree add worktrees/$WT_BRANCH -b "$WT_BRANCH" "$BASE_BRANCH"
```

3. 建立完成後需要同步將 claude/settings.local.json 一並複製到 worktree

## worktree 命名規則

錯誤處理：
- 使用既有 branch，但確保 reset 到正確的基礎分支
- 避免指向舊的 commit 而缺少必要檔案（如 openspec 資料夾）


## worktree structure:
   ```
   main-repo/
   ├── .git/
   └── worktrees/
       ├── wt-add-auth-system-20260110/
       ├── wt-refactor-api-20260111/
       └── wt-update-docs-20260113/
   ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z0890142) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
