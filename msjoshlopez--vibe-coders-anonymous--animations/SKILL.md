---
name: animations
description: Create gorgeous micro-interactions and animations using CSS and Svelte transitions. Use this skill when adding motion to components, page transitions, scroll animations, or any interactive elements that need polish. Use when this capability is needed.
metadata:
  author: msjoshlopez
---

# Animations Skill

This skill guides the creation of beautiful, purposeful animations using CSS and Svelte's built-in transition system. Great animations feel natural, add delight, and improve user experience.

## When to Use This Skill

- Adding hover states and micro-interactions
- Creating page transitions
- Animating elements on scroll
- Building loading states and skeletons
- Creating staggered list animations
- Adding entrance animations to sections
- Building interactive UI elements

## Philosophy

**Animations should be:**

- **Purposeful** - Every animation communicates something
- **Subtle** - Enhancement, not distraction
- **Fast** - Users shouldn't wait for animations
- **Consistent** - Same timing and easing throughout

**Avoid:**

- Animations that block user action
- Motion for motion's sake
- Jarring or unexpected movements
- Inconsistent timing across the site

---

## Timing & Easing Reference

### Standard Durations

| Type   | Duration  | Use Case                         |
| ------ | --------- | -------------------------------- |
| Micro  | 100-150ms | Button hovers, icon changes      |
| Fast   | 200-300ms | Fade ins, small movements        |
| Medium | 300-500ms | Page elements, cards             |
| Slow   | 500-800ms | Page transitions, large elements |

### Recommended Easings

```css
/* Smooth and natural */
--ease-out: cubic-bezier(0.33, 1, 0.68, 1);

/* Snappy entrance */
--ease-out-back: cubic-bezier(0.34, 1.56, 0.64, 1);

/* Elegant deceleration */
--ease-out-expo: cubic-bezier(0.16, 1, 0.3, 1);
```

---

## Svelte Transitions

### Built-in Transitions

```svelte
<script lang="ts">
	import { fade, fly, slide, scale, blur } from 'svelte/transition';

	let visible = $state(true);
</script>

{#if visible}
	<div transition:fade={{ duration: 300 }}>Fades in and out</div>
{/if}
```

### Common Transition Patterns

#### Fade In

```svelte
{#if visible}
	<div in:fade={{ duration: 300, delay: 0 }}>Content</div>
{/if}
```

#### Fly Up

```svelte
{#if visible}
	<div in:fly={{ y: 20, duration: 400 }}>Content slides up</div>
{/if}
```

#### Scale In

```svelte
{#if visible}
	<div in:scale={{ start: 0.95, duration: 300 }}>Content scales in</div>
{/if}
```

### Custom Transitions

```svelte
<script lang="ts">
	import { cubicOut } from 'svelte/easing';

	function customFly(node: HTMLElement, { delay = 0, duration = 400 }) {
		return {
			delay,
			duration,
			easing: cubicOut,
			css: (t: number) => `
        transform: translateY(${(1 - t) * 20}px);
        opacity: ${t};
      `
		};
	}
</script>

{#if visible}
	<div in:customFly={{ duration: 400 }}>Custom animation</div>
{/if}
```

---

## CSS Animation Patterns

### Hover Scale

```svelte
<button class="hover:scale-[1.02] active:scale-[0.98] transition-transform duration-150">
	Click me
</button>
```

### Fade Up on Mount (CSS)

```svelte
<div class="fade-up">Content</div>

<style>
	.fade-up {
		animation: fadeUp 0.5s cubic-bezier(0.33, 1, 0.68, 1) forwards;
	}

	@keyframes fadeUp {
		from {
			opacity: 0;
			transform: translateY(20px);
		}
		to {
			opacity: 1;
			transform: translateY(0);
		}
	}
</style>
```

### Staggered Children (CSS)

```svelte
<ul>
	{#each items as item, i}
		<li class="stagger-item" style="animation-delay: {i * 100}ms">
			{item}
		</li>
	{/each}
</ul>

<style>
	.stagger-item {
		opacity: 0;
		animation: fadeUp 0.4s cubic-bezier(0.33, 1, 0.68, 1) forwards;
	}

	.stagger-item:nth-child(1) {
		animation-delay: 0ms;
	}
	.stagger-item:nth-child(2) {
		animation-delay: 100ms;
	}
	.stagger-item:nth-child(3) {
		animation-delay: 200ms;
	}
	.stagger-item:nth-child(4) {
		animation-delay: 300ms;
	}

	@keyframes fadeUp {
		from {
			opacity: 0;
			transform: translateY(20px);
		}
		to {
			opacity: 1;
			transform: translateY(0);
		}
	}
</style>
```

### Loading Skeleton

```svelte
<div class="h-4 bg-gray-200 rounded skeleton"></div>

<style>
	.skeleton {
		animation: pulse 1.5s ease-in-out infinite;
	}

	@keyframes pulse {
		0%,
		100% {
			opacity: 0.5;
		}
		50% {
			opacity: 1;
		}
	}
</style>
```

---

## Scroll-Triggered Animations

### Using Intersection Observer

```svelte
<script lang="ts">
	import { onMount } from 'svelte';

	let element: HTMLElement;
	let isVisible = $state(false);

	onMount(() => {
		const observer = new IntersectionObserver(
			(entries) => {
				entries.forEach((entry) => {
					if (entry.isIntersecting) {
						isVisible = true;
						observer.unobserve(entry.target);
					}
				});
			},
			{ threshold: 0.1, rootMargin: '-50px' }
		);

		observer.observe(element);

		return () => observer.disconnect();
	});
</script>

<section
	bind:this={element}
	class="transition-all duration-500 ease-out {isVisible
		? 'opacity-100 translate-y-0'
		: 'opacity-0 translate-y-8'}"
>
	Content appears on scroll
</section>
```

### Reusable InView Component

```svelte
<!-- src/lib/components/InView.svelte -->
<script lang="ts">
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';

	interface Props {
		children: import('svelte').Snippet;
		threshold?: number;
		rootMargin?: string;
		once?: boolean;
	}

	let { children, threshold = 0.1, rootMargin = '-50px', once = true }: Props = $props();

	let element: HTMLElement;
	let isVisible = $state(false);
	let prefersReducedMotion = $state(false);

	onMount(() => {
		// Respect user's motion preferences
		prefersReducedMotion = window.matchMedia('(prefers-reduced-motion: reduce)').matches;

		if (prefersReducedMotion) {
			isVisible = true;
			return;
		}

		const observer = new IntersectionObserver(
			(entries) => {
				entries.forEach((entry) => {
					if (entry.isIntersecting) {
						isVisible = true;
						if (once) observer.unobserve(entry.target);
					} else if (!once) {
						isVisible = false;
					}
				});
			},
			{ threshold, rootMargin }
		);

		observer.observe(element);
		return () => observer.disconnect();
	});
</script>

<div
	bind:this={element}
	class="transition-all duration-500 ease-out {isVisible
		? 'opacity-100 translate-y-0'
		: 'opacity-0 translate-y-6'}"
>
	{@render children()}
</div>
```

---

## Page Transitions

### Using SvelteKit's Navigation

> **Version Requirement:** The `$app/state` module requires **SvelteKit 2.12+**. For earlier versions, use `$app/stores` with the `$page` store instead.

```svelte
<!-- src/routes/+layout.svelte -->
<script lang="ts">
	import { page } from '$app/state';
	import { fade } from 'svelte/transition';

	let { children } = $props();
</script>

{#key page.url.pathname}
	<div in:fade={{ duration: 200, delay: 200 }} out:fade={{ duration: 200 }}>
		{@render children()}
	</div>
{/key}
```

> **Note:** In SvelteKit 2.12+, use `$app/state` instead of `$app/stores`. The `page` object is reactive without needing the `$page` store prefix.

---

## Performance Tips

1. **Prefer `transform` and `opacity`** - These are GPU-accelerated:

   ```css
   /* Good */
   transform: translateX(10px);
   opacity: 0.5;

   /* Avoid */
   left: 10px;
   width: 100px;
   ```

2. **Use `will-change` sparingly:**

   ```css
   .animated-element {
   	will-change: transform;
   }
   ```

3. **Respect reduced motion:**

   ```css
   @media (prefers-reduced-motion: reduce) {
   	* {
   		animation-duration: 0.01ms !important;
   		transition-duration: 0.01ms !important;
   	}
   }
   ```

4. **Avoid animating on scroll without throttling** - Use Intersection Observer with `once: true`.

---

## Checklist Before Shipping

- [ ] Animations are fast (under 500ms for most)
- [ ] Consistent easing across the site
- [ ] Respects `prefers-reduced-motion`
- [ ] No layout shifts during animation
- [ ] Mobile performance tested
- [ ] Exit animations added where needed
- [ ] Stagger delays feel natural, not too slow

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/msjoshlopez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
