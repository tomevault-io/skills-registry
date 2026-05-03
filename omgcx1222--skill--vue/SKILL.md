---
name: vue
description: Use this skill when generating or modifying Vue code.
metadata:
  author: omgcx1222
---

# Vue

参考 [Vue 官方风格指南](https://vuejs.org/style-guide/) 和 Vue CLI 3 官方教程。

强制：

## 文件命名

自定义组件：template 中使用全小写连字符（<my-component>），JS 中使用小驼峰
普通文件多单词时使用小驼峰：helloWorld.js
.vue 文件使用大驼峰：HelloWorld.vue
文件夹使用大驼峰：TextTool
避免使用多级相对路径，使用路径别名
组件可自闭合时请自闭合

## Vue Router

每个路由定义都应有 name 属性
除首屏外，其他路由采用动态加载（懒加载）

## css

充分使用 scope 和标签
统一 flex grid 布局优先，避免老旧布局方式

---
> Source: [omgcx1222/skill](https://github.com/omgcx1222/skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
