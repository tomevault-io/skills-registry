---
name: project-page-structure
description: Documents the love-home (大熊小窝) uni-app page structure: main package vs subpackage, tab pages, layouts, and path conventions. Use when locating which file corresponds to which screen, adding or moving pages, or answering questions about routing and page organization. Use when this capability is needed.
metadata:
  author: baiyucraft
---

# 项目页面结构（大熊小窝）

用于快速定位「哪个页面属于什么结构」、主包/分包划分、以及路由与布局约定。

## 一、总览

| 类型 | 目录 | 说明 |
|------|------|------|
| **主包** | `src/pages/` | 仅 4 个 Tab 页，带底部 tabBar |
| **分包** | `src/subPages/` | 其余所有页面（登录、关于、打卡、碎碎念等） |
| **布局** | `src/layouts/` | `tabbar`（Tab 页）、`default`（分包等） |

路由由 `@uni-helper/vite-plugin-uni-pages` 根据 `src/pages` 与 `src/subPages` 自动生成；`pages.config.ts` 只配置 globalStyle 和 tabBar，不手写 pages 数组。

---

## 二、主包 Tab 页（`src/pages/`）

所有主包页面都使用 **layout: 'tabbar'**（见各页 `definePage({ layout: 'tabbar' })`）。

| 路径 | 路由名 | 标题 | 说明 |
|------|--------|------|------|
| `pages/index/index.vue` | home | 首页 | 周年倒计时、下一件重要的事、屎一下/必做事打卡/碎碎念入口 |
| `pages/food/index.vue` | food | 御膳房 | 菜谱分类、膳单弹窗、编辑菜单入口 |
| `pages/memories/index.vue` | memories | 回忆 | Banner、碎碎念列表入口 |
| `pages/me/me.vue` | me | 我的 | 头像、关于/登录/通知/个性化 |

- **访问路径**：`/pages/index/index`、`/pages/food/index`、`/pages/memories/index`、`/pages/me/me`
- **Tab 切换**：用 `router.pushTab({ name: 'home'|'food'|'memories'|'me' })`；Tab 列表与图标在 `src/composables/useTabbar.ts`（home/shop/heart/user）。

---

## 三、分包页（`src/subPages/`）

分包内页面使用 **default** 布局（无底部 tabBar），路径前缀 **`/subPages/`**。

| 目录/文件 | 访问路径 | 说明 |
|-----------|----------|------|
| `404/index.vue` | `/subPages/404/index` | 404 页 |
| `login/login.vue` | `/subPages/login/login` | 登录 |
| `login/register.vue` | `/subPages/login/register` | 注册 |
| `about/about.vue` | `/subPages/about/about` | 关于 |
| `about/alova.vue` | `/subPages/about/alova` | Alova 示例 |
| `check-in/index.vue` | `/subPages/check-in/index` | 打卡 |
| `demo/index.vue` | `/subPages/demo/index` | Demo |
| `demo/scroll.vue` | `/subPages/demo/scroll` | 滚动 Demo |
| `edit-menu/index.vue` | `/subPages/edit-menu/index` | 编辑菜单 |
| `important-things/index.vue` | `/subPages/important-things/index` | 重要事项 |
| `mutter/index.vue` | `/subPages/mutter/index` | 碎碎念 |
| `shopping-list/index.vue` | `/subPages/shopping-list/index` | 购物清单 |

跳转示例：`router.push('/subPages/login/login')`、`router.push('/subPages/about/about')`。

---

## 四、路由常量（`src/router/config.ts`）

- `LOGIN_PAGE` → `'/subPages/login/login'`
- `REGISTER_PAGE` → `'/subPages/login/register'`
- `NOT_FOUND_PAGE` → `'/subPages/404/index'`
- `EXCLUDE_LOGIN_PATH_LIST`：主包 4 个 Tab + 登录/注册/404 等免登录路径

新增需登录/免登录的页面时，在此维护。

---

## 五、布局与配置

- **tabbar 布局**（`src/layouts/tabbar.vue`）：内层用 `wd-config-provider` + `wd-tabbar` + `wd-gap`（底部安全区），供 4 个主包 Tab 使用。
- **default 布局**（`src/layouts/default.vue`）：仅 `wd-config-provider`，供分包及未指定 layout 的页面使用。
- **分包构建**：`vite.config.ts` 中 `UniHelperPages` 的 `subPackages: ['src/subPages']`，只此一个业务分包；不要使用 `pages-fg`、`pages-sub` 等其它分包根目录。
- **主题/标题**：`pages.config.ts` 的 `globalStyle.navigationBarTitleText` 为「大熊小窝」；各页可在 `definePage({ style: { navigationBarTitleText: '...' } })` 里覆盖。

---

## 六、快速查找表

| 想找的页面 | 位置 |
|------------|------|
| 首页（Tab） | `src/pages/index/index.vue` |
| 御膳房（Tab） | `src/pages/food/index.vue` |
| 回忆（Tab） | `src/pages/memories/index.vue` |
| 我的（Tab） | `src/pages/me/me.vue` |
| 登录/注册 | `src/subPages/login/login.vue`、`register.vue` |
| 关于 / Alova | `src/subPages/about/about.vue`、`alova.vue` |
| 打卡 / 重要事项 / 碎碎念 / 编辑菜单 / 购物清单 | `src/subPages/{check-in,important-things,mutter,edit-menu,shopping-list}/index.vue` |
| 404 | `src/subPages/404/index.vue` |
| Tab 列表与图标 | `src/composables/useTabbar.ts` |
| 路由常量 | `src/router/config.ts` |

新增页面时：若为 Tab 页则放在 `src/pages/` 并设 `layout: 'tabbar'`，且在 `pages.config.ts` 的 tabBar.list 增加一项；若为普通页则放在 `src/subPages/` 对应子目录下，路径即 `/subPages/子目录/文件名`。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/baiyucraft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
