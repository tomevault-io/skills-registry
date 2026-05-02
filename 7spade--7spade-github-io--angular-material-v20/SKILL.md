---
name: angular-material-v20
description: This skill helps GitHub Copilot understand how to assist with **Angular Material v20** UI components, design patterns, usage, and best practices. Use when this capability is needed.
metadata:
  author: 7spade
---
name: angular-material-v20
description: Skills for working with Angular Material v20 (@angular/material: "~20.0.0") UI component library in Angular applications.
license: MIT
---

# Angular Material v20 Skill

This skill helps GitHub Copilot understand how to assist with **Angular Material v20** UI components, design patterns, usage, and best practices.

## 📦 Package Info

- Package: `@angular/material`  
- Version Constraint: `~20.0.0`  
- Source: Official Angular Components repository  
- Peer dependencies: Angular core libraries (`@angular/core`, `@angular/common`, `@angular/forms`, `@angular/platform-browser`) and `@angular/cdk`. 0

## 🎯 What This Skill Covers

### 🎨 UI Components  
Provide assistance with importing and using **Material UI components** such as:
- Buttons, Icons, Toolbars, Menus
- Inputs, Form Controls (Checkbox, Radio, Select, Datepicker)
- Layout elements (Sidenav, Grid Lists)
- Navigation & Interaction patterns
- Data tables, pagination, sorting
- Dialogs, Snackbars, Tooltip  
*… and other MDC‑based Angular Material components.*

> Ensure correct Angular module imports for each component.

## 📐 Theming & Styling

Copilot should apply knowledge of:
- Angular Material theming principles (SCSS & CSS)
- Tokens and theming APIs
- Light/dark theme switch
- Custom theme generation  
> Material v20 embraces MDC and updated tokens for styling and theming.

## 🧪 Support Patterns

Help with:
- Writing usage examples in templates & TS files
- Handling Angular Material forms & reactive forms together
- Accessibility patterns and responsive layout
- Integration with Angular CLI  
> Material components aim to follow Material Design 3 and accessibility guidelines. 1

## 🔧 Best Practices

Encourage:
- Modular imports (`MatButtonModule`, `MatInputModule`, etc.)
- Correct `BrowserAnimationsModule` usage
- Using CDK utilities when needed

## 📚 Tips for Troubleshooting

Provide suggestions for:
- Dependency mismatches with Angular version
- Common runtime issues (animations, missing styles)
- Correct import setup in `app.module.ts`  
*Use official Angular Material docs and source as reference.*

---


---

📍 最佳放置路徑（給 Copilot Agent 讀）

你可以把這個 SKILL.md 放在專案內以下任一位置：

📁 專案層級技能（專案內通用）

/.github/skills/angular-material-v20/SKILL.md

> 📌 這樣 Copilot 就能在專案任何 Angular Material 相關時機注入技能提示。 




---

📁 全用戶共用技能（你的機器個人）

~/.copilot/skills/angular-material-v20/SKILL.md

> 📌 放在使用者個人 ~/.copilot/skills 下，就算跨專案也能用～ 🌈 




---

🧁 安裝 Angular Material v20 確認

要在 Angular 20 專案使用對應版本：

pnpm install @angular/material@~20.0.0

Material 20 的 docs & 程式碼都在 Angular Components 官方倉庫裡 👉 see GitHub repo. 


---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/7spade) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
