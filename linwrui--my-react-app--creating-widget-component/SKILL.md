---
name: creating-widget-component
description: 创建小部件组件，遵循项目规范，使用 TypeScript、类组件、Less 样式，通常接收颜色属性 Use when this capability is needed.
metadata:
  author: linwrui
---

# 创建小部件组件

## 描述
此技能用于在 `src/widgets/` 目录下创建小部件组件，遵循项目的技术栈和代码风格规范，通常用于显示特定功能的小型组件（如时间、天气等）。

## 使用场景
当用户需要创建小部件组件（如时间显示、天气显示、扑克牌显示等）时使用此技能。

## 指令

### 步骤 1：确定小部件位置
- 小部件组件应放置在 `src/widgets/` 目录下
- 使用 kebab-case 命名，如 `my-widget/index.tsx`
- 每个小部件是一个独立的子目录

### 步骤 2：创建小部件组件文件
- 使用类组件（Class Component）
- 导入 React 和必要的 Ant Design 组件
- 定义 Props 接口（通常包含 color 属性）
- 使用命名导出：`export class WidgetName extends React.Component<PropsType>`

### 步骤 3：定义类型接口
```typescript
interface PropsType {
  color?: string;  // 颜色属性，用于适配不同背景
  // 其他属性定义
}
```

### 步骤 4：实现小部件逻辑
- 在 constructor 中初始化 state（如需要）
- 在 componentDidMount 中启动定时器或获取数据（如需要）
- 在 componentWillUnmount 中清理定时器或取消请求
- 实现必要的处理方法（使用 private 修饰符）
- 使用 color 属性设置文字颜色

### 步骤 5：创建样式文件
- 在小部件目录下创建 `style.less` 文件
- 使用 Less 语法
- 使用语义化的类名，如 `widget-name`
- 在小部件文件中导入样式：`import './style.less'`

### 步骤 6：添加注释
- 使用 JSDoc 风格注释
- 为小部件添加功能说明
- 为方法添加参数和返回值说明
- 注释使用中文

## 示例

### 小部件组件示例 (src/widgets/my-widget/index.tsx)
```typescript
import React from 'react';
import './style.less';

interface PropsType {
  color?: string;
  data?: any;
}

export class MyWidget extends React.Component<PropsType> {
  private timer: NodeJS.Timeout | null = null;

  constructor(props: PropsType) {
    super(props);
  }

  componentDidMount() {
    this.startTimer();
  }

  componentWillUnmount() {
    this.stopTimer();
  }

  private startTimer = () => {
    this.timer = setInterval(() => {
      // 定时器逻辑
    }, 1000);
  };

  private stopTimer = () => {
    if (this.timer) {
      clearInterval(this.timer);
      this.timer = null;
    }
  };

  render() {
    const { color = '#333', data } = this.props;

    return (
      <div className="my-widget" style={{ color }}>
        <div className="widget-content">
          {/* 小部件内容 */}
        </div>
      </div>
    );
  }
}
```

### 样式文件示例 (src/widgets/my-widget/style.less)
```less
.my-widget {
  padding: 16px;
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);

  .widget-content {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 8px;
  }
}
```

## 注意事项
- 小部件通常接收 color 属性以适配不同背景
- 使用定时器时必须在 componentWillUnmount 中清理
- 优先使用 Ant Design 组件
- 图标使用 `@ant-design/icons` 或 `createFromIconfontCN` 创建的图标字体
- 小部件必须定义 TypeScript 类型
- 样式文件必须与组件文件分离
- 遵循项目的导入顺序规范
- 小部件应该轻量级，避免复杂逻辑

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/linwrui) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
