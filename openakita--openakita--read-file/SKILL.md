---
name: read-file
description: Read file content for text files. When you need to check file content, analyze code or data, or get configuration values. Use when this capability is needed.
metadata:
  author: openakita
---

# Read File

读取文件内容。

## Parameters

| 参数 | 类型 | 必填 | 说明 |
|-----|------|-----|------|
| path | string | 是 | 文件路径 |

## Examples

**读取配置文件**:
```json
{"path": "config.json"}
```

**读取代码文件**:
```json
{"path": "src/main.py"}
```

## Notes

- 适用于文本文件
- 使用 UTF-8 编码
- 二进制文件需要特殊处理

## Related Skills

- `write-file`: 写入文件
- `list-directory`: 列出目录内容

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openakita) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
