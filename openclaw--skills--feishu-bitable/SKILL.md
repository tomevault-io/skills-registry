---
name: feishu-bitable
description: 操作飞书多维表格（Bitable/Base）记录。 Use when this capability is needed.
metadata:
  author: openclaw
---
# Feishu Bitable Skill

操作飞书多维表格（Bitable/Base）记录。

## 功能
- 列出 Base 内的表
- 向表中新增记录/任务

## 使用方式
该技能目前主要作为库/模板使用：

```javascript
const { addRecord } = require('./add_task');
// addRecord(appToken, tableId, fields)
```

## 配置
- `FEISHU_APP_ID`
- `FEISHU_APP_SECRET`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
