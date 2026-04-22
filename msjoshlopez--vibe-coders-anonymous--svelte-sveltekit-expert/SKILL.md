---
name: svelte-sveltekit-expert
description: Expert guidance for SvelteKit 2 with Svelte 5 development. Use this skill when creating components, pages, layouts, or any Svelte/SvelteKit code. Ensures proper patterns for file-based routing, Svelte 5 runes, data loading, and Tailwind CSS styling. Use when this capability is needed.
metadata:
  author: msjoshlopez
---

# Svelte & SvelteKit Expert

Expert guidance for SvelteKit 2 with Svelte 5 (runes). This project uses static site generation.

## Version Requirements

This skill assumes:

- **Svelte 5.20+** (for `$props.id()`)
- **SvelteKit 2.12+** (for `$app/state`)
- **Tailwind CSS 4.x** (for `bg-linear-*` syntax)

Check your `package.json` to verify versions. Some features noted below require specific minimum versions.

## File-Based Routing

```
src/routes/
├── +page.svelte           → yoursite.com
├── about/+page.svelte     → yoursite.com/about
├── blog/[slug]/+page.svelte → yoursite.com/blog/any-post
├── +layout.svelte         → Wraps all pages (navigation, footer)
└── +error.svelte          → Error page
```

## Svelte 5 Runes

**ALWAYS use Svelte 5 runes syntax**, never the old Svelte 4 syntax:

```svelte
<script lang="ts">
	// Props (replaces "export let")
	let { name, count = 0 } = $props();

	// State (replaces "let x = 0")
	let counter = $state(0);

	// Derived values (replaces "$: doubled = count * 2")
	let doubled = $derived(counter * 2);

	// Effects (replaces "$: { ... }")
	$effect(() => {
		console.log('Counter changed:', counter);
	});
</script>
```

## Two-Way Binding with $bindable

For components that need two-way binding with their parent:

```svelte
<!-- src/lib/components/Slider.svelte -->
<script lang="ts">
	let { value = $bindable(50), min = 0, max = 100 } = $props();
</script>

<input type="range" bind:value {min} {max} />
<span>{value}</span>
```

Usage:

```svelte
<Slider bind:value={volume} />
```

## Unique IDs with $props.id()

> **Version Requirement:** `$props.id()` requires **Svelte 5.20.0+**. Check your package.json before using.

Generate consistent IDs for form elements (works with SSR):

```svelte
<script lang="ts">
	const id = $props.id();
</script>

<label for="{id}-email">Email</label>
<input id="{id}-email" type="email" />
```

## Component Patterns

**Page component:**

```svelte
<script lang="ts">
	// No props needed for pages typically
</script>

<main class="min-h-screen p-8">
	<h1 class="text-4xl font-bold">About Us</h1>
	<p class="mt-4 text-gray-600">Our story...</p>
</main>
```

**Reusable component with props:**

```svelte
<!-- src/lib/components/Button.svelte -->
<script lang="ts">
	interface Props {
		variant?: 'primary' | 'secondary';
		onclick?: () => void;
		children: import('svelte').Snippet;
	}

	let { variant = 'primary', onclick, children }: Props = $props();

	let styles = $derived(
		variant === 'primary' ? 'bg-blue-600 text-white' : 'bg-gray-200 text-gray-800'
	);
</script>

<button class="px-4 py-2 rounded {styles}" {onclick}>
	{@render children()}
</button>
```

## Using Children (Snippets)

Svelte 5 uses snippets instead of slots:

```svelte
<script lang="ts">
	let { children } = $props();
</script>

<div class="wrapper">
	{@render children()}
</div>
```

## Layout Pattern

```svelte
<!-- src/routes/+layout.svelte -->
<script lang="ts">
	import '../app.css';

	let { children } = $props();
</script>

<nav class="p-4 bg-gray-100">
	<a href="/">Home</a>
	<a href="/about" class="ml-4">About</a>
</nav>

{@render children()}

<footer class="p-4 bg-gray-100 mt-8">
	&copy; {new Date().getFullYear()} My Site
</footer>
```

## Tailwind CSS

Use Tailwind classes for all styling. Never create separate CSS files.

**Common patterns:**

```svelte
<!-- Spacing: p-4, m-2, px-6, py-3, mt-8, mb-4 -->
<!-- Text: text-xl, font-bold, text-gray-600, text-center -->
<!-- Layout: flex, grid, items-center, justify-between -->
<!-- Sizing: w-full, h-screen, max-w-4xl, min-h-screen -->
<!-- Colors: bg-blue-600, text-white, border-gray-300 -->
<!-- Responsive: md:flex-row, lg:text-xl, sm:p-4 -->
```

## Images

```svelte
<!-- From static folder -->
<img src="/logo.png" alt="Company Logo" class="w-48 h-auto" />

<!-- Or use enhanced:img for optimization (requires setup) -->
<enhanced:img src="/logo.png" alt="Company Logo" />
```

## Links

```svelte
<!-- For programmatic navigation -->
<script lang="ts">
	import { goto } from '$app/navigation';

	function navigate() {
		goto('/about');
	}
</script>

<!-- Standard anchor tags work in SvelteKit -->
<a href="/about" class="text-blue-600 hover:underline">About Us</a>
```

## Event Handlers

Use lowercase event names:

```svelte
<button onclick={() => count++}>Click me</button>
<input oninput={(e) => (name = e.currentTarget.value)} />
<form onsubmit={handleSubmit}>...</form>
```

## Conditional Rendering

```svelte
{#if condition}
	<p>Shown when true</p>
{:else if otherCondition}
	<p>Alternative</p>
{:else}
	<p>Fallback</p>
{/if}
```

## Lists

```svelte
{#each items as item, index (item.id)}
	<div>{index}: {item.name}</div>
{/each}
```

## Common Mistakes to Avoid

1. **Don't use Svelte 4 syntax** - No `export let`, no `$:` reactivity
2. **Don't create .html files** - This is Svelte, use .svelte
3. **Don't use `<script>` without `lang="ts"`** - Always use TypeScript
4. **Don't create .css files** - Use Tailwind classes
5. **Don't forget to use runes** - `$state`, `$derived`, `$effect`, `$props`
6. **Don't use `on:click`** - Use `onclick` (Svelte 5 syntax)
7. **Don't use `<slot>`** - Use snippets with `{@render children()}`

## Debugging with $inspect

Use `$inspect` to log reactive values during development (automatically stripped in production):

```svelte
<script lang="ts">
	let count = $state(0);
	let user = $state({ name: 'Alice' });

	// Logs whenever count or user changes
	$inspect(count);
	$inspect(user);

	// With custom label
	$inspect('User state:', user);
</script>
```

> **Note:** `$inspect` only works during development. It's removed from production builds.

---

## Reading State Without Tracking

Use `untrack()` when you need to read a value without creating a reactive dependency:

```svelte
<script lang="ts">
	import { untrack } from 'svelte';

	let count = $state(0);
	let lastSaved = $state(0);

	$effect(() => {
		// This effect runs when count changes
		// but reading lastSaved won't re-trigger it
		const current = count;
		const previous = untrack(() => lastSaved);
		console.log(`Changed from ${previous} to ${current}`);
	});
</script>
```

---

## Advanced State Patterns

### $state.raw() - Large Data Without Deep Reactivity

For large arrays or objects where you don't need deep reactivity (better performance):

```svelte
<script lang="ts">
	// No deep proxy overhead - only reassignment triggers updates
	let items = $state.raw<Item[]>([]);

	async function loadItems() {
		const data = await fetch('/api/items').then((r) => r.json());
		items = data; // Reassignment triggers update, mutation does not
	}
</script>
```

### $state.snapshot() - For External Libraries

When passing state to libraries that don't expect proxies (like `structuredClone`, analytics, or APIs):

```svelte
<script lang="ts">
	let formData = $state({ name: '', email: '' });

	async function submit() {
		// Get a plain object, not a proxy
		const data = $state.snapshot(formData);
		await fetch('/api/submit', {
			method: 'POST',
			body: JSON.stringify(data)
		});
	}
</script>
```

### $derived.by() - Complex Derivations

For derivations that need more than a single expression:

```svelte
<script lang="ts">
	let items = $state<Item[]>([]);

	let stats = $derived.by(() => {
		const total = items.length;
		const completed = items.filter((i) => i.done).length;
		return { total, completed, remaining: total - completed };
	});
</script>
```

---

## Svelte 4 vs Svelte 5 Quick Reference

| Svelte 4                 | Svelte 5                              |
| ------------------------ | ------------------------------------- |
| `export let prop`        | `let { prop } = $props()`             |
| `let count = 0`          | `let count = $state(0)`               |
| `$: doubled = count * 2` | `let doubled = $derived(count * 2)`   |
| `$: { console.log(x) }`  | `$effect(() => { console.log(x) })`   |
| `on:click={handler}`     | `onclick={handler}`                   |
| `<slot />`               | `{@render children()}`                |
| N/A                      | `$bindable()` for two-way binding     |
| N/A                      | `$props.id()` for unique IDs (5.20+)  |
| N/A                      | `$state.raw()` for large data         |
| N/A                      | `$state.snapshot()` for external libs |
| N/A                      | `$inspect()` for debugging            |
| N/A                      | `untrack()` to read without tracking  |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/msjoshlopez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
