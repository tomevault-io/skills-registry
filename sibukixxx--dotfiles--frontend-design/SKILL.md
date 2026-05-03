---
name: frontend-design
description: | Use when this capability is needed.
metadata:
  author: sibukixxx
---

# Frontend Design

Create production-quality frontend interfaces that are visually distinctive, well-structured, and maintainable.

## Core Principles

### Design Quality
- Avoid generic "AI-generated" aesthetics (excessive gradients, overused patterns)
- Use whitespace intentionally for visual hierarchy
- Ensure consistent spacing, typography scales, and color harmony
- Design for real content, not lorem ipsum placeholders

### Technical Standards
- Write semantic HTML with proper accessibility (ARIA labels, keyboard navigation)
- Use responsive design (mobile-first approach recommended)
- Optimize for performance (minimal dependencies, efficient CSS)
- Follow component-based architecture for reusability

### Technology Selection

| Context | Recommended Stack |
|---------|------------------|
| Interactive apps, SPAs | React + Tailwind CSS |
| Simple static pages | HTML + CSS (vanilla) |
| Vue ecosystem projects | Vue 3 + Tailwind CSS |
| Rapid prototyping | React + shadcn/ui |

Default to **React + Tailwind CSS** when no specific requirement exists.

Design patterns, workflow details, and anti-patterns are in `references/design-patterns.md`.

## Troubleshooting

### レスポンシブが崩れる場合
mobile-first で実装し、ブレークポイントを段階的に追加。Tailwind の `sm:`, `md:`, `lg:` を活用。

### アクセシビリティの問題
`axe-core` や Lighthouse でチェック。ARIA ラベル、キーボードナビ、コントラスト比を確認。

### パフォーマンスが遅い場合
不要な依存を削除。画像は最適化。CSS-in-JS より Tailwind を推奨。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sibukixxx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
