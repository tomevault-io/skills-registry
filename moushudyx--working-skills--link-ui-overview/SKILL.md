---
name: link-ui-overview
description: link工程PC端开发说明与link-ui组件库使用说明 Use when this capability is needed.
metadata:
  author: moushudyx
---

## 工程结构

此工程基于 Vue 2.6 和 link 专用组件库开发，系统主要分为 CRM 和 DMS 两大部分

- src/modules/crm - CRM 系统相关代码
- src/modules/dms - DMS 系统相关代码

页面接入不依赖本地路由注册, 按菜单URL动态加载, 新增页面时务必关注下文的“示例”章节

页面代码文件路径按照 src/modules/{crm|dms}/{模块名称}/{页面名称}/{页面名称}-{list|detail}.vue 组织(菜单需要到线上环境配置, 配置菜单的操作一般由用户完成, 如果用户不会配置或者出问题了, 参考 references/nav.md 文档指导用户)

- 比如 src/modules/crm/rebate_manage/rebate_product/rebate-product-list.vue 表示 CRM 系统的返利管理(rebate_manage)模块下的返利商品(rebate_product)页面的列表页(rebate-product-list)

系统中可能存在不按此规范命名的页面文件，请根据实际情况处理

## 常见概念

ID: link 项目中所有数据都一个 id 字段, 是一个永远不会重复的长数字字段(一般当作字符串处理), 关联子表时往往使用 headId 来指示自己的父级数据

安全性: **(重要概念)**在PC端配置, 每个菜单都有安全性控制, link-table 等组件会自动响应相关配置, 变更自己的默认查询参数、默认按钮等; 比如某个页面对某个用户配置了“我组织及下级组织数据”的安全性, 那么列表查询时的 oauth 参数会自动编程"MT_ORG"(或者"MY_ORG_拼接一串特殊参数"), 如果页面安全性配置了不允许编辑, 那么列表的编辑按钮会自动隐藏(还有双击编辑功能也会失效)

值列表: **(重要概念)**也称 lov, 部分来自 O2 项目的同事可能称其为“值集”, 用于将存在后端的英文数据转换成人类阅读的文本(如后端存"NEW"、"SUBMITTED"前端展示“新建”“已提交”, 其中后端存的部分叫“独立源代码”, 展示的值叫“显示值”, lov 的编码叫“lov类型”或者“值集编码”或其他什么名称), 在 PC 端的“值列表”页面配置, 使用时需要用到 link-lov 或其他名称类似的组件, 组件会根据传入的 lov-type 自动请求后端接口, 获取“值-展示值”的配置

Picklist: **(重要概念)**也称 object 选择, 部分来自 O2 项目的同事可能称其为“值集视图”, 打开弹框, 里面是一个列表, 用户勾选数据并点击确定, 有单选和多选两种模式, 使用起来比较复杂, 建议使用时参考已有代码处理

MVG: (不常用的组件)穿梭框组件, 点开弹框, 分为左右两个列表, 点确定时会保存在 MVG 专用表里(通过 mvgParentId 关联对应的数据); 此组件使用非常麻烦, 需要配置左右两个框的 option, 建议使用时参考已有代码处理; 关键信息: mvgParentId - 绑定的数据 id, mvgName - MVG 模板名称

## link 组件库

此组件库没有对外公开文档，请参考已有代码进行使用；此组件不通过 npm 安装，而是直接放在 static/lib/link 下

页面中使用组件库的组件（特征是以 link- 开头的自定义标签）时不需要引入

相关文档字数太多, 建议直接看 references 目录下的文档, 下面是一个简单的目录结构说明, 根据实际需求查阅需要的文档, 避免 token 用量过多

### 组件

- 按钮 link-button, 文档见 references/link-button.md
- 对话框 link-dialog, 文档见 references/link-dialog.md
- **(重要组件)**表单 link-form-panel, 文档见 references/link-form.md
  - 编写详情页面时, 也可参考此文档
  - 表单相关组件 link-form-item、link-form-grid、link-panelfolder 等, 也参考此文档
  - 一些旧组件 link-form、lnk-form-panel 可能还在部分页面使用, 也可参考此文档
  - 校验相关参考 references/link-form-validate.md 文档
- 图标 link-icon, 文档见 references/link-icon.md
- 输入框 link-input, 文档见 references/link-input.md
- 数字输入框 link-input-number, 文档见 references/link-input-number.md
- 文本域 link-textarea, 文档见 references/link-textarea.md
- 单选/多选 link-radio, 文档见 references/link-radio.md
- 下拉选择 link-select, 文档见 references/link-select.md
- 日期选择 link-datepicker, 文档见 references/link-datepicker.md
- 时间选择 link-timepicker, 文档见 references/link-timepicker.md
- 步骤条 link-steps, 文档见 references/link-steps.md
- 树组件 link-tree-pro, 文档见 references/link-tree-pro.md
- 地址选择 link-address-input/link-table-column-address, 文档见 references/link-address.md
- 对象选择（picklist）link-object-input/link-table-column-object, 文档见 references/link-object.md
- 多对多选择（MVG）link-mvg-input/link-table-column-mvg, 文档见 references/link-mvg.md
- **(重要组件)**值集相关组件, 文档见 references/link-lov.md
  - 常见组件名 link-table-column-lov、link-radio-lov、link-lov-select、link-lov-text
- 图片轮播 link-slider, 文档见 references/link-slider.md
- **(重要组件)**列表 link-auto-table, 文档见 references/link-table.md
  - 编写列表页面时, 也可参考此文档
  - 列表相关组件 link-table-column-*, 文档见 references/link-table-column.md
  - 校验相关参考 references/link-form-validate.md 文档

### 服务

- 对话框服务 $dialog, 文档见 references/link-dialog.md
- 气泡提示服务 $msg, 文档见 references/link-msg.md
- 图片预览 $slider, 文档见 references/link-slider.md
- 对象选择服务 $object, 文档见 references/link-object.md
- 多对多选择服务 $mvg, 文档见 references/link-mvg.md
- 值列表服务 $lov, 文档见 references/link-lov.md
- 地址选择服务 $lv.$address, 文档见 references/link-address.md

### 示例

- 单列表页、列表-详情结构、父子表等结构, 文档见 references/page-list.md
- 详情页(表单页)、列表-详情结构、详情页 tab 等结构, 文档见 references/page-detail.md

常用 mixin

- globalPublicMixin, 文档见 references/globalPublicMixin.md, 包含一些方法如 publicHandler returnBack operateTip initFormOption 等

### 常见功能

- 页面导航与菜单配置, 文档见 references/nav.md

## 公共对象

### linkTools

上面有很多方法但是基本只用到这几种

- deepCopy(obj) 深拷贝对象，只能拷贝普通的对象和数组
- isEmpty(val) 判断值是否为空，null、undefined、空字符串、空数组、空对象都会返回 true
- copyToClipboard(text) 复制文本到剪贴板，返回true/false 表示是否成功

### appCtx

appCtx 是一个全局的应用上下文对象，常用的参数就以下几个

- appName 应用名称，比如 CRM、DMS
- isAdmin 是否为管理员用户，Y/N 类型
- orgId 当前用户所属组织ID
- orgName 当前用户所属组织名称
- positionType 当前用户职位类型, SysAdmin 系统管理员
- userId 当前用户ID
- userName 当前用户名称

## 其他注意事项

如果用户要求按照设计文档(或者叫业务文档)编写页面, 对于设计文档中标为删除的部分, 可以考虑忽略, 也可以正常编写但是注释掉该部分代码, 以免遗漏重要的逻辑或者配置

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/moushudyx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
