---
name: construction-skill
description: 使用 VoxelBuilder API 生成 JavaScript 代码来建造结构。在建筑之务必使用此技能来查看和创建实际的建筑代码。 Use when this capability is needed.
metadata:
  author: justcnds
---

# 📝 建造技能

使用 VoxelBuilder API 生成 JavaScript 代码来建造建筑。

## VoxelBuilder API

### 基础方块操作
| 方法 | 用途 |
|------|------|
| `builder.set(x, y, z, block)` | 放置单个方块 |
| `builder.get(x, y, z)` | 获取指定位置方块 |
| `builder.fill(x1, y1, z1, x2, y2, z2, block)` | 填充立方体区域 |
| `builder.walls(x1, y1, z1, x2, y2, z2, block)` | 建四面墙（空心） |
| `builder.line(x1, y1, z1, x2, y2, z2, block)` | 画直线/斜线 |
| `builder.clear(...)` | 清空区域 |

### 分组与优先级
| 方法 | 用途 |
|------|------|
| `builder.beginGroup(name, { priority })` | 开始分组 |
| `builder.endGroup()` | 结束分组 |
| `builder.setPriority(n)` | 设置优先级 |

优先级: door=100, frame=95, windows=70, roof=60, walls=50, decor=20

### 门窗
| 方法 | 用途 |
|------|------|
| `builder.setDoor(x, y, z, type)` | 放置双格门 |

### 屋顶
| 方法 | 用途 |
|------|------|
| `builder.drawRoofBounds(x1,y,z1,x2,z2,h,style,mat,opts)` | 矩形屋顶 |
| `builder.drawPolyRoof(x,y,z,r,h,sides,style,mat)` | 多边形屋顶 |

style: straight/gambrel/arch/cone/dome/curve/steep
opts: { gable: '材料', ridge: '屋脊材料' }

### 几何体
| 方法 | 用途 |
|------|------|
| `builder.drawCylinder(x,y,z,r,h,mat)` | 圆柱体 |
| `builder.drawSphere(x,y,z,r,mat)` | 球体 |
| `builder.drawEllipsoid(x,y,z,rx,ry,rz,mat)` | 椭球体 |
| `builder.drawPolygon(x,y,z,r,sides,h,mat)` | 多边形柱 |
| `builder.drawPyramid(x,y,z,base,h,mat)` | 金字塔 |
| `builder.drawTorus(x,y,z,R,r,mat)` | 圆环 |

options: { hollow, thickness, axis, noise }

### 曲线与装饰
| 方法 | 用途 |
|------|------|
| `builder.drawBezier(points,mat,w)` | 贝塞尔曲线 |
| `builder.scatter(x1,y,z1,x2,z2,d,types)` | 2D散布花草 |
| `builder.scatter3D(...)` | 3D空间散布 |
| `builder.drawHanging(x,y,z,opts)` | 单点悬挂物 |
| `builder.drawHangingRing(x,y,z,r,opts)` | 环形悬挂 |
| `builder.drawSpiralStairs(x,y,z,r,h,mat)` | 螺旋楼梯 |

### 组件系统
| 方法 | 用途 |
|------|------|
| `builder.defineComponent(name,fn)` | 定义组件 |
| `builder.placeComponent(name,x,y,z)` | 放置组件 |

options: { rotateY: 0/90/180/270 }

### 方块属性语法
```
'stone'                              // 普通方块
'oak_stairs?facing=north'            // 楼梯朝向
'oak_door?facing=south,half=lower'   // 门下半
'oak_log?axis=x'                     // 原木横放
'lantern?hanging=true'               // 悬挂灯笼
```

## 使用方法

想了解具体用法，请使用 `read_subdoc` 查阅资源文档。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justcnds) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
