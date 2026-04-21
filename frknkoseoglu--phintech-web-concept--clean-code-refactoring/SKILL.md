---
name: clean-code-refactoring
description: Guidelines for code structure, file limits, and DRY principles. Use this to review code before finalizing. Use when this capability is needed.
metadata:
  author: frknkoseoglu
---

# 🧹 Clean Code & Refactoring Policy

We write code for humans first, computers second.

## 📏 Size Limits
-   **Server Actions:** Max 50-60 lines. If longer, extract logic to `src/lib/services/`.
-   **Components:** If a component has >3 `useState` or `useEffect`, extract logic to a Custom Hook (`useStockData.ts`).

## ♻️ DRY (Don't Repeat Yourself)
-   **Magic Numbers:** No `if (status === 1)`. Use Enums: `if (status === OrderStatus.PENDING)`.
-   **Utils:** Common formatting (Dates, Currency) must be in `src/lib/utils.ts`.

## 🏗️ Separation of Concerns
-   **UI Components:** Should only care about *displaying* data.
-   **Server Actions:** Should only care about *mutating* data.
-   **Services:** Should only care about *business logic*.

**Refactoring Trigger:**
If you find yourself copying and pasting a block of code -> STOP. Make it a function.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/frknkoseoglu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
