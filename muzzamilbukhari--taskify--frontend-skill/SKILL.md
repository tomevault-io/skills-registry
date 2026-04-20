---
name: frontend-skill
description: description: Build scalable frontend pages, layouts, and reusable components using Next.js App Router and Tailwind CSS. Use when this capability is needed.
metadata:
  author: muzzamilbukhari
---
---
name: frontend-skill
description: Build scalable frontend pages, layouts, and reusable components using Next.js App Router and Tailwind CSS.
---

# Frontend Skill – Next.js App Router & Tailwind CSS

## Instructions

1. **Project Structure**
   - Use Next.js App Router (`/app` directory)
   - Organize routes with folders (`page.tsx`, `layout.tsx`)
   - Separate UI components into `/components`

2. **Layouts & Pages**
   - Create root and nested layouts
   - Use shared layouts for dashboards and auth flows
   - Implement loading and error states (`loading.tsx`, `error.tsx`)

3. **Components**
   - Build reusable, composable components
   - Follow atomic/component-driven design
   - Use props for flexibility and reusability

4. **Styling with Tailwind CSS**
   - Utility-first styling approach
   - Responsive design using breakpoints
   - Dark mode support with Tailwind config
   - Use `clsx` or `cn` helpers for conditional classes

5. **Navigation & UI**
   - Use `Link` for client-side navigation
   - Implement responsive navbar and footer
   - Consistent spacing, typography, and colors

## Best Practices
- Mobile-first responsive design
- Keep components small and focused
- Avoid inline styles; prefer Tailwind utilities
- Use semantic HTML elements
- Optimize for accessibility (ARIA, contrast, focus states)
- Co-locate styles and logic within components

## Example Structure
```tsx
// app/page.tsx
export default function HomePage() {
  return (
    <main className="flex min-h-screen items-center justify-center bg-gray-100">
      <section className="max-w-2xl text-center p-6">
        <h1 className="text-4xl font-bold mb-4">
          Build Fast with Next.js
        </h1>
        <p className="text-gray-600 mb-6">
          Modern frontend using App Router and Tailwind CSS
        </p>
        <button className="px-6 py-3 bg-black text-white rounded-lg hover:bg-gray-800">
          Get Started
        </button>
      </section>
    </main>
  );
}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/muzzamilbukhari) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
