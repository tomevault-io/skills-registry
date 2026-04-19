---
name: wt-resolve
description: 自动检测并解决 Git 分支与远程的冲突。优先尝试自动合并，无法自动解决时提示用户。触发条件：用户输入 "/wt-resolve" 或请求解决冲突/同步远程代码。 Use when this capability is needed.
metadata:
  author: lxxyx
---

# wt-resolve

自动检测当前分支与远程的冲突，优先自动解决，无法自动解决时提示用户。

## 工作流程

1. **获取当前分支和远程信息**
2. **检查是否有冲突**
3. **尝试自动合并**
4. **处理冲突文件**（如有）
5. **完成合并**

## 执行步骤

### 1. 获取分支信息

```bash
# 获取当前分支
git branch --show-current

# 获取远程分支信息
git fetch origin

# 检查当前分支与远程的差异
git log --oneline --left-right HEAD...origin/<current-branch> 2>/dev/null || echo "no remote"
```

### 2. 检查冲突

```bash
# 尝试合并（不实际执行）
git merge-tree $(git merge-base HEAD origin/<current-branch>) HEAD origin/<current-branch>

# 或尝试 rebase
git rebase --dry-run origin/<current-branch> 2>&1
```

### 3. 自动合并策略

尝试按以下顺序解决：

```bash
# 方法1：快进合并（最简单）
git merge --ff-only origin/<current-branch>

# 方法2：普通合并
git merge origin/<current-branch> --no-edit

# 方法3：变基（如果分支未推送）
git pull --rebase origin <current-branch>
```

### 4. 处理冲突文件

如果自动合并失败，列出冲突文件：

```bash
git diff --name-only --diff-filter=U
```

对每个冲突文件：

1. **读取冲突标记**，分析冲突内容
2. **判断解决策略**：
   - 如果是简单的重复变更，选择适当版本
   - 如果是复杂逻辑冲突，告知用户具体冲突位置

3. **标记已解决**：
```bash
git add <file>
```

### 5. 完成合并

```bash
# 完成合并（如果是 merge）
git commit --no-edit

# 或继续变基（如果是 rebase）
git rebase --continue

# 或中止变基（如用户要求）
git rebase --abort
```

## 冲突解决优先级

1. **无冲突**：直接合并，完成
2. **可自动解决**：接受远程版本或自动合并
3. **需要人工介入**：
   - 显示冲突文件列表
   - 显示每处冲突的具体内容
   - 询问用户选择（本地/远程/手动编辑）

## 示例输出

```
检查远程分支 origin/feat/login...
发现 3 个提交需要同步
尝试自动合并... ✅
已成功合并，无冲突
```

```
检查远程分支 origin/feat/login...
发现 1 个文件存在冲突：src/auth.ts
冲突位置：第 45-52 行
请选择解决方案：
1. 保留本地版本
2. 接受远程版本
3. 手动编辑
```

## 注意事项

- 优先尝试 `git merge`，失败时尝试 `git rebase`
- 如果变基过程中有冲突，提供 `--abort` 选项
- 合并前检查是否有未提交的更改
- 合并后验证是否成功（`git status`）

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lxxyx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
