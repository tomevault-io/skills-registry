---
name: decoration-skill
description: 添加装饰元素。包括植被、家具、灯光、做旧效果。 Use when this capability is needed.
metadata:
  author: justcnds
---

# 🎨 Decoration Skill

为建筑添加装饰元素。

## 使用时机

- 主体结构完成后
- 用户要求添加装饰
- 检查报告缺少内饰/外饰时

## 装饰类型

### 植被
```javascript
// 地面花草
builder.scatter(-15, 1, -15, 15, 15, 0.2, ['poppy', 'dandelion']);

// 悬挂藤蔓
builder.drawHangingRing(0, 10, 0, 8, { length: 5, type: 'vine' });
```

### 家具
| 房间 | 元素 |
|------|------|
| 卧室 | bed, chest, lantern, carpet |
| 厨房 | smoker, cauldron, barrel |
| 客厅 | stairs(座椅), crafting_table, flower_pot |
| 书房 | bookshelf, lectern, lantern |

### 灯光
- `lantern` - 中世纪/乡村
- `soul_lantern` - 哥特/阴森
- `campfire` - 壁炉/烟囱
- `sea_lantern` - 现代/奇幻

### 做旧效果
```javascript
// 混入苔藓变体
// 外墙添加藤蔓
builder.scatter(-10, 2, -10, 10, 8, 0.15, ['vine']);
```

## 优先级

装饰使用低优先级，避免覆盖结构：
```javascript
builder.beginGroup('decoration', { priority: 20 });
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justcnds) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
