---
name: microsoft-todo
description: Manage tasks and lists in Microsoft To Do on Windows via PowerShell and Microsoft Graph API. Create, complete, and organize tasks with My Day and reminders. Use when this capability is needed.
metadata:
  author: malue-ai
---

# Microsoft To Do 任务管理（Windows）

通过 PowerShell 操作 Windows 内置的 Microsoft To Do 应用。
支持创建任务、管理列表、设置提醒、标记完成。

## 使用场景

- 用户说「帮我添加一个待办」「今天要做什么」「标记 XX 任务完成」
- 用户需要整理任务清单
- 用户需要设置提醒或截止日期
- 与 Outlook 日历/邮件联动的任务管理

## 命令参考

### 通过 URI 协议快速操作

```powershell
# 打开 To Do 应用
Start-Process "ms-todo:"

# 快速添加任务（打开 To Do 并聚焦到新建）
Start-Process "ms-todo://create"

# 打开「我的一天」视图
Start-Process "ms-todo://myday"
```

### 通过 Microsoft Graph API（PowerShell）

```powershell
# ===== 前置：获取访问令牌 =====
# 使用设备码流登录（首次使用时执行一次）
$clientId = "YOUR_APP_CLIENT_ID"  # 需在 Azure AD 注册应用
$scope = "Tasks.ReadWrite"
$body = @{
    client_id = $clientId
    scope     = $scope
}
$deviceCode = Invoke-RestMethod -Uri "https://login.microsoftonline.com/consumers/oauth2/v2.0/devicecode" -Method POST -Body $body
Write-Output "请打开 $($deviceCode.verification_uri) 并输入代码: $($deviceCode.user_code)"

# ===== 列出所有任务列表 =====
$headers = @{ Authorization = "Bearer $token" }
$lists = Invoke-RestMethod -Uri "https://graph.microsoft.com/v1.0/me/todo/lists" -Headers $headers
$lists.value | ForEach-Object { Write-Output "$($_.id) — $($_.displayName)" }

# ===== 列出某列表中的任务 =====
$listId = "LIST_ID"
$tasks = Invoke-RestMethod -Uri "https://graph.microsoft.com/v1.0/me/todo/lists/$listId/tasks" -Headers $headers
$tasks.value | Where-Object { $_.status -ne "completed" } | ForEach-Object {
    Write-Output "[ ] $($_.title) $(if($_.dueDateTime){'📅 '+$_.dueDateTime.dateTime})"
}

# ===== 创建新任务 =====
$newTask = @{
    title = "完成季度报告"
    dueDateTime = @{
        dateTime = "2025-03-15T17:00:00"
        timeZone = "Asia/Shanghai"
    }
    importance = "high"
    body = @{
        content     = "包括销售数据和趋势分析"
        contentType = "text"
    }
} | ConvertTo-Json -Depth 3
Invoke-RestMethod -Uri "https://graph.microsoft.com/v1.0/me/todo/lists/$listId/tasks" -Headers $headers -Method POST -Body $newTask -ContentType "application/json"

# ===== 标记任务完成 =====
$taskId = "TASK_ID"
$update = @{ status = "completed" } | ConvertTo-Json
Invoke-RestMethod -Uri "https://graph.microsoft.com/v1.0/me/todo/lists/$listId/tasks/$taskId" -Headers $headers -Method PATCH -Body $update -ContentType "application/json"
```

### 简化方案（无需 API，通过 URI + 剪贴板）

```powershell
# 用 URI 协议 + 剪贴板实现快速添加
$task = "完成季度报告"
Set-Clipboard -Value $task
Start-Process "ms-todo://create"
# 提示用户：已复制到剪贴板，To Do 打开后 Ctrl+V 粘贴
```

## 输出规范

- 任务列表用清单格式：`[ ] 未完成任务` / `[x] 已完成任务`
- 显示截止日期、重要性标记
- 创建/完成操作后确认结果
- 「我的一天」视图优先展示

## 安全规则

- **删除列表前必须 HITL 确认**
- 不批量标记完成（逐个确认）
- 不修改其他用户共享的列表

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/malue-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
