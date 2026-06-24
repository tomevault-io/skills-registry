---
name: ui-change
description: 修改 UI 组件（页面入口、风格规范、图标库约定）。 Use when this capability is needed.
metadata:
  author: zxj719
---

## 主要 UI 文件

| 页面 | 文件 |
|---|---|
| 登录/注册 | `src/components/Auth/` |
| 个人主页 | `src/components/Dashboard.jsx` |
| 游戏设置 | `src/components/SetupScreen.jsx` |
| 游戏主界面 | `src/components/GameArena.jsx` |
| Token 管理 | `src/components/TokenManager.jsx` |
| 用户战绩 | `src/components/UserStats.jsx` |

## 样式约定

- TailwindCSS
- 暗色主题：`bg-zinc-950` / `text-zinc-100`
- 边框：`border-zinc-700` / `border-zinc-800`
- 卡片：`bg-zinc-900 rounded-xl border border-zinc-700`

## 图标

使用 `lucide-react`：

```jsx
import { Settings } from 'lucide-react';
<Settings size={20} className="text-zinc-400" />
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zxj719) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
