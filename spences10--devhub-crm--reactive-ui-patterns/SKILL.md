---
name: reactive-ui-patterns
description: Remote functions reactive UI patterns. Use for smooth in-place updates, preventing page jumps, and managing loading states with .current property. Use when this capability is needed.
metadata:
  author: spences10
---

# Reactive UI Patterns

## Quick Start

```svelte
<script lang="ts">
	const data_query = get_data(); // Store in variable for .current access

	async function save(id: string, value: string) {
		await update_data({ id, value });
		await data_query.refresh(); // Updates in place!
	}
</script>

{#if data_query.error}
	<p>Error loading data</p>
{:else if data_query.loading && data_query.current === undefined}
	<p>Loading...</p>
{:else}
	{@const items = data_query.current ?? []}
	<div class:opacity-60={data_query.loading}>
		{#each items as item}<!-- Content updates smoothly -->{/each}
	</div>
{/if}
```

## Core Principles

- **Store queries**: `const query = get_data()` enables `.current`
  property access
- **Use `.current`**: Prevents page jumps, keeps scroll position
  during updates
- **Initial load only**: Show spinner when `.current === undefined`,
  not on every refresh
- **Avoid `{#await}`**: Causes jarring page reloads - use stored query
  pattern instead

## Reference Files

- [current-property.md](references/current-property.md) - Deep dive on
  `.current` property
- [anti-patterns.md](references/anti-patterns.md) - Common mistakes to
  avoid
- [examples.md](references/examples.md) - Real-world implementation
  examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spences10) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
