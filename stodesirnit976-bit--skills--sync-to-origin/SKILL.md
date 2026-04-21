---
name: sync-to-origin
description: 在任意 Git 仓库中，把当前电脑的工作区/本地分支强制对齐到远程 origin（把 origin 当唯一真源）。用于两台电脑来回切换开发、需要快速消除不同步/分支混乱时：自动备份本地状态（bundle+diff+可选未跟踪文件），然后 fetch --prune，删除远程不存在的本地分支，并将所有本地分支重置为对应的 origin/<branch>。 Use when this capability is needed.
metadata:
  author: stodesirnit976-bit
---

# Sync To Origin

## 目标

把“这台（可能落后/混乱的）电脑”无脑对齐到 `origin` 的最新状态，做到：
- 工作区内容与 `origin` 对应分支一致（`reset --hard` + `clean`）
- 本地所有分支与 `origin/*` 分支一致（强制移动分支指针）
- 删除本地那些 **远程已不存在** 的分支（例如领先电脑已合并并删分支）

这会丢弃本机任何“未推送到 origin”的东西，但会先做可恢复备份。

## 前提（重要）

- “领先电脑”的成果需要已经在 GitHub 上（也就是已经推到 `origin`）。否则落后电脑不可能仅靠同步就得到那些只存在于另一台电脑本地的提交/分支。
- 本 skill **不会** 自动把本机内容推到远程；它只做“从远程对齐到本机”。

## 你会得到的备份

脚本会在 Git 目录下创建：`.git/codex-sync-backups/<timestamp>/`
- `repo.bundle`：包含本地所有 refs 的离线备份（可用 `git clone repo.bundle` 或 `git fetch repo.bundle <ref>` 恢复）
- `diff.patch` / `staged.patch`：未提交改动的补丁（即使后面清理了也能找回）
- `untracked.zip`（默认开启）：未跟踪文件的压缩备份（可用 `-SkipUntrackedBackup` 关闭）

## 关键安全规则（强制）

- 不允许强推（`push --force*`）到远程：`origin` 永远是裁决者
- 这是破坏性同步：脚本默认会要求显式确认（`-Yes`）才会执行

## 用法

在你要同步的仓库根目录运行：

```powershell
pwsh -NoProfile -ExecutionPolicy Bypass -File custom/sync-to-origin/scripts/sync-to-origin.ps1 -Yes
```

可选参数：
- `-SkipUntrackedBackup`：不备份未跟踪文件（更快）
- `-NoClean`：不执行 `git clean -ffd`（只重置已跟踪文件）

说明：
- `git clean -ffd` 只会清理未跟踪文件/目录，不会删除 ignored 文件；如果你需要把 ignored 产物也清掉，请在确认理解风险后手动用 `git clean -ffdx`。

## 常见失败与处理

- 找不到 `origin`：先 `git remote -v`，设置正确远程后再跑
- 远程默认分支检测失败：先在本机执行 `git remote set-head origin -a`，或手动设置远程 HEAD
- 有 submodule：建议先在脚本跑完后再 `git submodule sync --recursive` + `git submodule update --init --recursive`

## 如何从备份恢复（紧急用）

- 恢复分支/提交：在 repo 目录执行 `git fetch <backupDir>/repo.bundle 'refs/*:refs/*'`
- 查看补丁：用编辑器打开 `diff.patch` / `staged.patch`；或 `git apply --stat diff.patch`

## Codex CLI / Windows 沙箱提示

如果脚本里某些 `git` 操作报 `Access is denied` / 无法写 `.git`，在 Codex CLI 里需要用提升权限的执行环境重试（理由：该操作需要写入 `.git` 元数据）。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stodesirnit976-bit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
