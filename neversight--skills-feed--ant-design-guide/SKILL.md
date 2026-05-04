---
name: ant-design-guide
description: 包含 Ant Design (Antd) 所有核心组件的官方文档索引及最佳实践。用于查询组件 API、Props 定义及最新特性。 Use when this capability is needed.
metadata:
  author: neversight
---

# Ant Design Component Guide

当用户询问有关 Ant Design 组件的使用、样式修改、或者需要编写特定 UI 功能时，请参考此指南。

## ⚠️ 关键指令 (Critical Instructions)
1.  **查阅文档**: Ant Design 版本更新较快（当前主流为 v5/v6），遇到复杂的 Props（如 Table columns, Form rules, Upload customRequest）时，**必须使用浏览工具 (Browsing Tool)** 读取对应组件的 URL 以获取准确的类型定义。
2.  **V5/V6 风格**: 默认使用 Functional Components + Hooks。优先使用 CSS-in-JS (Antd Style) 或 `style` 属性，避免使用旧版的 Less 导入方式，除非用户特定要求。
3.  **App 包裹**: 在使用 `message`, `modal`, `notification` 的静态方法时，推荐使用 `<App>` 组件包裹应用，并使用 `App.useApp()` hook 获取实例，以确保样式和 Context 正确继承。

---

## 📚 常用框架集成 (Integration)
*如果用户询问如何初始化项目或配置环境：*
- **Vite**: [https://ant.design/docs/react/use-with-vite](https://ant.design/docs/react/use-with-vite)
- **Next.js**: [https://ant.design/docs/react/use-with-next](https://ant.design/docs/react/use-with-next)
- **Umi**: [https://ant.design/docs/react/use-with-umi](https://ant.design/docs/react/use-with-umi)
- **Remix/SSR**: [https://ant.design/docs/react/server-side-rendering](https://ant.design/docs/react/server-side-rendering)

---

## 🧩 组件索引 (Component Index)

### 1. 通用与布局 (General & Layout)
*构建页面骨架和基础元素*
- **Button (按钮)**: [https://ant.design/components/button](https://ant.design/components/button)
- **Icon (图标)**: [https://ant.design/components/icon](https://ant.design/components/icon)
- **Typography (排版)**: [https://ant.design/components/typography](https://ant.design/components/typography)
- **Layout (布局)**: [https://ant.design/components/layout](https://ant.design/components/layout)
- **Grid (栅格)**: [https://ant.design/components/grid](https://ant.design/components/grid)
- **Flex (弹性布局)**: [https://ant.design/components/flex](https://ant.design/components/flex)
- **Space (间距)**: [https://ant.design/components/space](https://ant.design/components/space)
- **ConfigProvider (全局配置)**: [https://ant.design/components/config-provider](https://ant.design/components/config-provider)

### 2. 导航 (Navigation)
*页面跳转与层级导航*
- **Menu (菜单)**: [https://ant.design/components/menu](https://ant.design/components/menu)
- **Breadcrumb (面包屑)**: [https://ant.design/components/breadcrumb](https://ant.design/components/breadcrumb)
- **Dropdown (下拉菜单)**: [https://ant.design/components/dropdown](https://ant.design/components/dropdown)
- **Steps (步骤条)**: [https://ant.design/components/steps](https://ant.design/components/steps)
- **Pagination (分页)**: [https://ant.design/components/pagination](https://ant.design/components/pagination)

### 3. 数据录入 (Data Entry)
*表单与交互控件*
- **Form (表单总线)**: [https://ant.design/components/form](https://ant.design/components/form)
- **Input / Textarea**: [https://ant.design/components/input](https://ant.design/components/input)
- **Select (选择器)**: [https://ant.design/components/select](https://ant.design/components/select)
- **Radio / Checkbox**: [https://ant.design/components/radio](https://ant.design/components/radio) / [https://ant.design/components/checkbox](https://ant.design/components/checkbox)
- **DatePicker (日期选择)**: [https://ant.design/components/date-picker](https://ant.design/components/date-picker)
- **Upload (上传)**: [https://ant.design/components/upload](https://ant.design/components/upload)
- **Switch (开关)**: [https://ant.design/components/switch](https://ant.design/components/switch)
- **TreeSelect (树选择)**: [https://ant.design/components/tree-select](https://ant.design/components/tree-select)
- **Transfer (穿梭框)**: [https://ant.design/components/transfer](https://ant.design/components/transfer)

### 4. 数据展示 (Data Display)
*核心展示组件*
- **Table (表格)**: [https://ant.design/components/table](https://ant.design/components/table) - *注意：复杂表格推荐优先考虑 ProTable*
- **List (列表)**: [https://ant.design/components/list](https://ant.design/components/list)
- **Descriptions (描述列表)**: [https://ant.design/components/descriptions](https://ant.design/components/descriptions)
- **Card (卡片)**: [https://ant.design/components/card](https://ant.design/components/card)
- **Tabs (标签页)**: [https://ant.design/components/tabs](https://ant.design/components/tabs)
- **Tag (标签)**: [https://ant.design/components/tag](https://ant.design/components/tag)
- **Image (图片)**: [https://ant.design/components/image](https://ant.design/components/image)
- **Tree (树形控件)**: [https://ant.design/components/tree](https://ant.design/components/tree)
- **QRCode (二维码)**: [https://ant.design/components/qr-code](https://ant.design/components/qr-code)
- **Statistic (统计数值)**: [https://ant.design/components/statistic](https://ant.design/components/statistic)

### 5. 反馈与交互 (Feedback)
*交互反馈*
- **Modal (对话框)**: [https://ant.design/components/modal](https://ant.design/components/modal)
- **Drawer (抽屉)**: [https://ant.design/components/drawer](https://ant.design/components/drawer)
- **Message (全局提示)**: [https://ant.design/components/message](https://ant.design/components/message)
- **Notification (通知提醒)**: [https://ant.design/components/notification](https://ant.design/components/notification)
- **Spin (加载中)**: [https://ant.design/components/spin](https://ant.design/components/spin)
- **Popconfirm (气泡确认)**: [https://ant.design/components/popconfirm](https://ant.design/components/popconfirm)
- **Skeleton (骨架屏)**: [https://ant.design/components/skeleton](https://ant.design/components/skeleton)
- **Watermark (水印)**: [https://ant.design/components/watermark](https://ant.design/components/watermark)

### 6. 主题定制 (Theme)
- **Customize Theme**: [https://ant.design/docs/react/customize-theme](https://ant.design/docs/react/customize-theme)
- **ColorPicker**: [https://ant.design/components/color-picker](https://ant.design/components/color-picker)

---

## 💡 使用场景示例

1.  **表单场景**: 结合 `Form`, `Input`, `Select`, `Button`。使用 `Form.useForm()` 获取实例。
2.  **后台列表**: 结合 `Table`, `Pagination`, `Space` (操作栏)。
3.  **详情页**: 结合 `Descriptions` 或 `Card`。
4.  **全局反馈**: 使用 `<App>` 组件包裹根节点，在子组件中使用 `App.useApp()` 调用 `message` 或 `modal`。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
