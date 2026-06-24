---
name: config-management
description: When agent config parameters need querying or modification → read/write YAML configuration files. Use when this capability is needed.
metadata:
  author: cklxx
---

# config-management

查询和修改 agent YAML 配置文件。

## 调用

```bash
python3 skills/config-management/run.py get --key llm.model
python3 skills/config-management/run.py set --key llm.model --value gpt-4o
python3 skills/config-management/run.py list
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cklxx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
