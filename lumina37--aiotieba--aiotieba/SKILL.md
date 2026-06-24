---
name: proto-compile
description: >- Use when this capability is needed.
metadata:
  author: lumina37
---

# Protobuf编译Skill

## 触发时机

变更或新增`.proto`文件时

## 使用方法

```bash
python scripts/proto_compile.py -a            # 编译全部
python scripts/proto_compile.py -c            # 仅编译公共protobuf定义
python scripts/proto_compile.py -d <api_name> # 编译指定名称的API模块
python scripts/proto_compile.py -c -d A -d B  # 组合使用
```

---
> Source: [lumina37/aiotieba](https://github.com/lumina37/aiotieba) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
