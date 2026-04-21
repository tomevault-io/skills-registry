---
name: inspection-skill
description: 分析现有建筑代码和场景。检查结构问题、计算边界、为新建筑定位。 Use when this capability is needed.
metadata:
  author: justcnds
---

# 🔍 Inspection Skill

分析和检查现有建筑代码。

## 使用时机

- 修改现有代码前（了解当前结构）
- 添加新建筑到现有场景时（避免重叠）
- 用户报告问题时

## 可用脚本

### analyzeScene.js
分析当前场景的边界框，用于多建筑定位。

```json
{ "name": "run_script", "arguments": { "script": "analyzeScene", "passCurrentCode": true } }
```

返回：
```json
{
  "bounds": { "minX": 0, "maxX": 20, "maxZ": 20 },
  "size": { "width": 21, "height": 30 },
  "recommendation": "Place new structure at X=30"
}
```

### analyzeStructure.js
检查代码中的常见问题（缺少内饰、无窗户等）。

## 常见问题修复

**山墙缝隙**: 添加循环填充三角形墙面

**空内部**: 使用 decoration_skill 添加家具

**无窗框**: 添加活板门作为百叶窗

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justcnds) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
