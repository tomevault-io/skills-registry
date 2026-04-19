---
name: magicui-design
description: Standardizes the adaptation of Magic UI components to the Curlew Finance "Organic/Editorial" aesthetic. Use this skill when implementing dynamic UI elements, animations, or micro-interactions. Use when this capability is needed.
metadata:
  author: alanlee0323
---

# Magic UI Design Adaptation

## Overview
This skill defines how to adapt **Magic UI** components (typically high-tech/neon) to fit the **Curlew Finance** (Organic, Tactile, Muted) brand.

## Core Principles
1.  **No Neon**: Replace default Cyan/Purple glows with **Terracotta (`#C66A46`)**, **Sage (`#8BA88E`)**, or **Warm Charcoal (`#424242`)**.
2.  **Texture over Gradient**: Prefer subtle noise textures or paper-like effects over smooth, digital gradients.
3.  **Gentle Motion**: Slow down animations. "Luxury is slow." (e.g., increase durations by 50-100%).
4.  **Matte Finish**: Avoid high-gloss reflections.

## Component Adaptations

### 1. Shine Border (Organic Version)
Used for emphasizing important cards (e.g., "Danger" or "Opportunity" insights).
*   **Color**: Use `calico-terracotta` or `calico-sage` with low opacity (20-40%).
*   **Speed**: Slower duration (`8s` or more).
*   **Width**: Thinner beam size.

### 2. Marquee / Ticker
Used for displaying live data or news.
*   **Background**: Transparent or `calico-oatmeal` with multiply blend mode.
*   **Gap**: Generous spacing between items (`gap-8`+).
*   **Speed**: `[--duration:40s]` (Slow and readable).

### 3. Number Ticker
Used for dashboard statistics.
*   **Font**: Use `Playfair Display` (Serif) for the numbers to give a "Financial Report" feel.
*   **Color**: `calico-terracotta` (for emphasis) or `calico-charcoal` (standard).

## Implementation Rules
*   **Tailwind Config**: Ensure `animation` and `keyframes` in `tailwind.config.ts` support the slower durations.
*   **Z-Index**: Ensure animations (like `GridPattern`) stay behind the content (`-z-10`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alanlee0323) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
