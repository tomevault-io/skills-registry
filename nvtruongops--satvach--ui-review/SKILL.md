---
name: ui-review
description: Guidelines for Professional UI/UX & Frontend Code Review (Vercel-like Standards) Use when this capability is needed.
metadata:
  author: nvtruongops
---

# UI/UX & Frontend Review Guidelines - Dự Án Sát Vách

Skill này giúp agent thực hiện **Review & Refactor** frontend code (SolidJS + Tailwind + Flowbite) để đạt chất lượng "Vercel-level": **Professional, Fast, Accessible, & Aesthetically Pleasing**.

## 1. Design & Aesthetics Audit (The "Vercel Look")

Khi review hoặc generate UI, hãy kiểm tra các yếu tố sau:

### 🎨 Visual Hierarchy & Spacing

- **Whitespace**: Sử dụng whitespace hào phóng để tạo cảm giác "sang trọng" và dễ đọc. Tránh UI quá chật chội.
- **Consistency**: Kiểm tra padding/margin xem có tuân thủ scale của Tailwind không (e.g., `p-4`, `p-6`, `gap-4`). Tránh magic numbers (e.g., `13px`).
- **Depth**: Sử dụng bóng (shadows) tinh tế (`shadow-sm`, `shadow-md`) và borders mỏng (`border-gray-200`, `dark:border-gray-700`) để phân tách lớp.

### 💎 "Premium" Polish

- **Glassmorphism**: Sử dụng `backdrop-blur` cho overlays hoặc sticky headers nếu phù hợp.
- **Micro-interactions**: Tất cả các elements tương tác (buttons, links, cards) **PHẢI** có trạng thái `:hover`, `:active`, và `:focus-visible`.
  - _Good_: `hover:bg-gray-50 active:scale-95 transition-all`
- **Typography**: Sử dụng đúng weight để phân cấp (Bold cho headers, Medium/Regular cho body). Màu text nên là `text-gray-900` (primary) và `text-gray-500` (secondary), tránh đen tuyền (`#000`).

## 2. User Experience (UX) Review

### 🚀 Perceived Performance

- **Loading States**: **KHÔNG** để màn hình trắng. Sử dụng **Skeleton Loaders** (xương) thay vì spinners đơn điệu cho content chính.
- **Feedback**: Mọi hành động (Click, Submit) đều phải có phản hồi tức thì (Toast, disabled state, animation).
- **Empty States**: Luôn xử lý trường hợp không có dữ liệu (Empty Data) một cách đẹp mắt (Icon + Text + Call to Action).

### ♿ Accessibility (A11y)

- **Contrast**: Đảm bảo text đủ tương phản với nền.
- **Focus**: **KHÔNG BAO GIỜ** xoá outline mặc định (`outline-none`) mà không thay thế bằng custom focus ring (`focus:ring-2`).
- **Semantic HTML**: Sử dụng đúng thẻ (`<button>` vs `<a>`, `<main>`, `<nav>`, `<h1>`-`<h6>`).
- **Alt Text**: Mọi `<img>` phải có `alt`.

## 3. SolidJS & Code Quality Review

### ⚛️ Reactivity Check

- **Signal Unwrapping**: Kiểm tra xem signals có được gọi như hàm không: `count()` ✅ vs `count` ❌.
- **Effect Risks**: Tránh lạm dụng `createEffect` để update UI derived từ state khác (dùng `createMemo` hoặc derived signals).
- **Index vs ID**: Khi dùng `<For>`, ưu tiên dùng item thực tế, tránh dùng index nếu list có thể thay đổi thứ tự.

### 🛠 Tailwind Code Style

- **Clarity**: Sort classes một cách hợp lý (Layout -> Box Model -> Typography -> Visuals -> Interaction).
- **Avoid Arbitrary Values**: Hạn chế dùng `w-[350px]`. Hãy dùng `w-full max-w-sm`.

## 4. Review Checklist (Copy & Paste khi Review)

Sử dụng checklist này để đánh giá PR hoặc Code Snippet:

```markdown
### 🎨 Visual & UX

- [ ] **Spacing/Layout**: Có thoáng đãng và nhất quán không?
- [ ] **Interactions**: Button/Link có hover/active states không?
- [ ] **Loading/Empty**: Có xử lý loading và empty state không?
- [ ] **Mobile**: Có responsive trên mobile không (hidden overflow, touch targets)?

### 🛡️ Code Quality

- [ ] **Reactivity**: Signals được dùng đúng cách? Không mất reactivity?
- [ ] **Cleanup**: Có clear timers/listeners trong `onCleanup` không?
- [ ] **Types**: Không dùng `any`? Props interface rõ ràng?
- [ ] **A11y**: Có `aria-label` cho icon buttons không? Keyboard navigable?
```

## 5. Example "Vercel-Style" Component

```tsx
import { Component, Show } from "solid-js";

interface CardProps {
  title: string;
  description?: string;
  isLoading?: boolean;
  onClick?: () => void;
}

export const PremiumCard: Component<CardProps> = (props) => {
  return (
    <div
      class="group relative border border-gray-200 dark:border-gray-800 rounded-lg p-6 
             bg-white dark:bg-black hover:border-gray-400 transition-colors duration-200 
             shadow-sm hover:shadow-md cursor-pointer"
      onClick={props.onClick}
    >
      <Show
        when={!props.isLoading}
        fallback={<div class="animate-pulse h-20 bg-gray-100 rounded" />}
      >
        <h3 class="text-lg font-semibold text-gray-900 dark:text-gray-100 group-hover:text-blue-600 transition-colors">
          {props.title}
        </h3>
        <Show when={props.description}>
          <p class="mt-2 text-sm text-gray-500 dark:text-gray-400 leading-relaxed">
            {props.description}
          </p>
        </Show>

        {/* Decorative arrow interaction */}
        <div class="absolute top-6 right-6 opacity-0 group-hover:opacity-100 transition-opacity -translate-x-2 group-hover:translate-x-0 duration-200">
          →
        </div>
      </Show>
    </div>
  );
};
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nvtruongops) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
