---
name: windows-clipboard
description: Read from and write to the Windows clipboard using PowerShell. Use when this capability is needed.
metadata:
  author: malue-ai
---

# Windows 剪贴板

使用 PowerShell 操作 Windows 剪贴板。

## 命令参考

### 读取剪贴板

```powershell
Get-Clipboard
```

### 写入剪贴板

```powershell
Set-Clipboard -Value "要复制的内容"
```

### 从文件复制到剪贴板

```powershell
Get-Content "C:\path\to\file.txt" | Set-Clipboard
```

## 输出规范

- 读取后展示内容（长文本截断前 500 字符）
- 写入后确认「已复制到剪贴板」

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/malue-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
