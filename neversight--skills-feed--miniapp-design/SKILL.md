---
name: miniapp-design
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# 小程序设计工作流 Skill

## 概述

此 Skill 定义了 **Obsidian + AI 直接生成代码** 的标准操作流程，替代传统的 Figma 设计流程。

## 核心文件

| 文件 | 路径 | 用途 |
|------|------|------|
| 页面设计文档 | `apps/teamventure/docs/design/miniapp-pages-obsidian.md` | 所有页面的结构化描述 |
| Design Tokens | `apps/teamventure/design/tokens/*.json` | 颜色、间距、字体等样式变量 |
| 小程序代码 | `apps/teamventure/src/frontend/miniapp/pages/` | WXML/WXSS/JS 源码 |

## 标准操作流程 (SOP)

### Phase 1: 明确需求

1. 确认要修改的页面（login/index/myplans/comparison/detail/profile/home）
2. 确认修改类型：
   - 布局调整
   - 新增组件
   - 交互变更
   - 样式修改

### Phase 2: 更新设计文档

1. 读取 `miniapp-pages-obsidian.md` 中对应页面的 section
2. 使用 ASCII 图更新布局结构
3. 更新交互细节或状态流转
4. 如涉及新字段，更新数据结构

**示例：添加组件**
```markdown
### 布局结构
\`\`\`
┌─────────────────────────────┐
│   [🔐 微信一键登录]        │
│                             │
│        先逛逛 →             │  ← 新增：游客入口
│                             │
└─────────────────────────────┘
\`\`\`

### 状态流转
6. **点击"先逛逛"** → 以游客模式进入首页  ← 新增
```

### Phase 3: 生成代码

根据更新后的设计描述，修改三个文件：

#### 3.1 WXML（结构）
```xml
<!-- 根据布局图添加元素 -->
<view class="guest-entry" bindtap="handleGuestMode">
  <text class="guest-text">先逛逛</text>
  <text class="guest-arrow">→</text>
</view>
```

#### 3.2 WXSS（样式）
```css
/* 参考 design/tokens/ 中的变量 */
.guest-entry {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-top: 32rpx;  /* spacing.scale.4 */
}

.guest-text {
  font-size: 28rpx;   /* typography.fontSize.base */
  color: rgba(255, 255, 255, 0.8);
}
```

#### 3.3 JS（逻辑）
```javascript
handleGuestMode() {
  // 根据状态流转描述实现逻辑
  app.globalData.isGuestMode = true
  wx.switchTab({ url: '/pages/index/index' })
}
```

### Phase 4: 验证

1. 在微信开发者工具中预览效果
2. 确认视觉和交互符合设计描述
3. 如有差异，更新设计文档保持同步

## 页面清单与限界上下文

| 页面 | BC | 聚合 | 设计文档锚点 |
|------|----|----|-------------|
| login | Identity & Session | User | `[[login]]` |
| index | Planning | PlanRequest | `[[index]]` |
| myplans | Planning | Plan | `[[myplans]]` |
| comparison | Planning | Plan | `[[comparison]]` |
| detail | Planning | Plan, SupplierContactLog | `[[detail]]` |
| profile | Identity + Planning | User + 读模型 | `[[profile]]` |
| home | 展示层 | - | `[[home]]` |

## Design Tokens 速查

### 颜色
```
--primary: #1890FF
--success: #52C41A
--warning: #FAAD14
--error: #FF4D4F
--text-primary: #262626
--text-secondary: #8C8C8C
```

### 间距
```
8rpx / 16rpx / 24rpx / 32rpx / 48rpx
```

### 圆角
```
8rpx (按钮) / 12rpx (输入框) / 16rpx (卡片) / 40rpx (胶囊)
```

### 阴影
```
0 2rpx 12rpx rgba(0,0,0,0.05)  /* 轻 */
0 4rpx 12rpx rgba(0,0,0,0.08)  /* 中 */
```

## 使用示例

### 示例 1：修改现有页面

```
用户: 给登录页添加一个游客入口

Claude:
1. [读取设计文档 [[login]] section]
2. [更新布局图，添加"先逛逛"入口]
3. [更新状态流转，添加游客模式流程]
4. [修改 login.wxml 添加 guest-entry 元素]
5. [修改 login.wxss 添加样式]
6. [修改 login.js 添加 handleGuestMode 方法]
```

### 示例 2：新增组件

```
用户: 在方案详情页添加分享按钮

Claude:
1. [读取设计文档 [[detail]] section]
2. [在底部操作栏布局中添加分享按钮]
3. [添加交互说明：点击分享 → 调用微信分享 API]
4. [修改 detail.wxml/wxss/js]
```

### 示例 3：样式调整

```
用户: 把卡片圆角改成更圆润的

Claude:
1. [查看 tokens/radius.json]
2. [确认当前使用 16rpx，改为 24rpx]
3. [更新设计文档中的 Design Tokens section]
4. [批量修改相关 wxss 文件]
```

## 注意事项

1. **先更新文档，再改代码** - 保持设计文档与代码同步
2. **使用 Design Tokens** - 不要硬编码颜色、间距等值
3. **遵循限界上下文** - 新功能放到正确的 BC 中
4. **保持 ASCII 图简洁** - 用于快速理解，不需要像素级精确

## 相关资源

- 设计文档：`apps/teamventure/docs/design/miniapp-pages-obsidian.md`
- Design Tokens：`apps/teamventure/design/tokens/`
- DDD 设计：`apps/teamventure/docs/design/strategy-and-ddd.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
