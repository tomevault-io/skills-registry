---
name: creating-react-component
description: 创建通用 React 组件，遵循项目规范，使用 TypeScript、类组件和 Less 样式 Use when this capability is needed.
metadata:
  author: linwrui
---

# 创建通用 React 组件

## 描述
此技能用于在 `src/components/` 目录下创建可复用的通用 React 组件，遵循项目的技术栈和代码风格规范。

## 使用场景
当用户需要创建通用组件（如按钮、表单、对话框等）时使用此技能。

## 指令

### 步骤 1：确定组件位置
- 组件应放置在 `src/components/` 目录下
- 使用 kebab-case 命名，如 `my-component/index.tsx`

### 步骤 2：创建组件文件
- 使用类组件（Class Component）而非函数组件
- 导入 React 和必要的 Ant Design 组件
- 定义 Props 和 State 接口
- 使用命名导出：`export class ComponentName extends React.Component<PropsType, StateType>`

### 步骤 3：定义类型接口
```typescript
interface PropsType {
  // 组件属性定义
}

interface StateType {
  // 组件状态定义
}
```

### 步骤 4：实现组件逻辑
- 在 constructor 中初始化 state
- 使用生命周期方法（componentDidMount、componentWillUnmount 等）
- 实现必要的处理方法（使用 private 修饰符）
- 在 componentWillUnmount 中清理副作用

### 步骤 5：创建样式文件
- 在组件目录下创建 `style.less` 文件
- 使用 Less 语法
- 使用语义化的类名
- 在组件文件中导入样式：`import './style.less'`

### 步骤 6：添加注释
- 使用 JSDoc 风格注释
- 为组件添加功能说明
- 为方法添加参数和返回值说明
- 注释使用中文

## 示例

### 组件文件示例 (src/components/my-component/index.tsx)
```typescript
import React from 'react';
import { Button } from 'antd';
import './style.less';

interface PropsType {
  title: string;
  onClick?: () => void;
}

interface StateType {
  loading: boolean;
}

export class MyComponent extends React.Component<PropsType, StateType> {
  constructor(props: PropsType) {
    super(props);
    this.state = {
      loading: false,
    };
  }

  private handleClick = () => {
    const { onClick } = this.props;
    if (onClick) {
      onClick();
    }
  };

  render() {
    const { title } = this.props;
    const { loading } = this.state;

    return (
      <div className="my-component">
        <Button loading={loading} onClick={this.handleClick}>
          {title}
        </Button>
      </div>
    );
  }
}
```

### 样式文件示例 (src/components/my-component/style.less)
```less
.my-component {
  display: flex;
  align-items: center;
  justify-content: center;

  .ant-btn {
    margin: 10px;
  }
}
```

## 注意事项
- 优先使用 Ant Design 组件
- 图标使用 `@ant-design/icons` 或 `createFromIconfontCN` 创建的图标字体
- 组件必须定义 TypeScript 类型
- 样式文件必须与组件文件分离
- 遵循项目的导入顺序规范

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/linwrui) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
