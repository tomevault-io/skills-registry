---
name: tmux-manager
description: 該 skill 建立、取得現有、發送指令等相關 tmux session 操作 Use when this capability is needed.
metadata:
  author: z0890142
---

# tmux-manager

## 檢查 tmux 特定 session 是否存在

1. 需要提供 <session_name>
2. 使用 script 確認特定 session 是否存在
```sh
sh check_tmux_sessions.sh <session_name>
```

## 建立特定名稱的 session 
1. 需要提供 <session_name>
2. 使用 script 建立特定 session
```sh
sh create_tmux_sessions.sh <session_name>
```

## 指派任務給 tmux session 規則

1. 需要提供 <session_name>、<worktree_path> 與 <openspec_proposal>

```sh
sh run_worker.sh <session_name> <worktree_path> <openspec_proposal>
```

## 刪除特定名稱的 session 
1. 需要提供 <session_name>
2. 使用 script 刪除特定 session
```sh
sh remove_tmux_session.sh <session_name>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z0890142) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
