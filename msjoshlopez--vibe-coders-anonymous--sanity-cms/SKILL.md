---
name: sanity-cms
description: On-demand Sanity CMS setup for editable content. Guides through initialization, schema creation, and Studio configuration. Triggers when user wants to make content editable. Use when this capability is needed.
metadata:
  author: msjoshlopez
---

# Sanity CMS Skill

This skill guides users through adding Sanity CMS to make their website content editable through a friendly admin interface.

## When to Trigger

- User asks to "add a CMS"
- User wants to "edit content without code"
- User mentions "content management"
- User asks about "making text/images editable"
- User wants to "update content themselves"

## MCP Tools Available

This project has a Sanity MCP server configured. Check for available Sanity tools by looking at the MCP server list. If Sanity MCP tools are available, use them for:

- Creating/updating schemas
- Managing content
- Querying data

Use the manual setup below only if MCP tools aren't available or for initial project setup.

## What is Sanity? (Explain Simply)

> "Sanity is like a Google Doc for your website. You edit text and images in a friendly dashboard, and your website automatically updates. No coding needed after we set it up."

Key benefits for non-technical users:

- Edit text like a Word document
- Upload and manage images easily
- Changes go live instantly
- Can't break the website by editing content

---

## Setup Flow

### Step 1: Create Sanity Account

If they don't have Sanity set up yet:

> "First, let's get you a Sanity account. Go to **sanity.io** and sign up—it's free for small sites."

Wait for them to confirm account creation.

### Step 2: Initialize Sanity in Project

```bash
npx sanity@latest init --env
```

This will prompt for:

- Project name (use their site name)
- Default dataset configuration: Yes
- Project output path: `/sanity` or `/studio`
- TypeScript: Yes
- Package manager: npm

### Step 3: Install SvelteKit Dependencies

```bash
# Required - the Sanity client
npm install @sanity/client

# Optional - only if you need image URL transformations
npm install @sanity/image-url
```

### Step 4: Environment Variables

Create/update `.env`:

```env
PUBLIC_SANITY_PROJECT_ID="[from sanity init]"
PUBLIC_SANITY_DATASET="production"
PUBLIC_SANITY_API_VERSION="2026-01-01"
```

> **Note:** The API version should use the current date (YYYY-MM-DD format). Update this to today's date when setting up a new project.

### Step 5: Create Sanity Client

Create `src/lib/sanity.ts`:

**Basic setup (text content only):**

```typescript
import { createClient } from '@sanity/client';

export const client = createClient({
	projectId: import.meta.env.PUBLIC_SANITY_PROJECT_ID,
	dataset: import.meta.env.PUBLIC_SANITY_DATASET,
	apiVersion: import.meta.env.PUBLIC_SANITY_API_VERSION,
	useCdn: true // Set to false for preview/draft content
});
```

**With image support (requires `@sanity/image-url`):**

```typescript
import { createClient } from '@sanity/client';
import imageUrlBuilder from '@sanity/image-url';

export const client = createClient({
	projectId: import.meta.env.PUBLIC_SANITY_PROJECT_ID,
	dataset: import.meta.env.PUBLIC_SANITY_DATASET,
	apiVersion: import.meta.env.PUBLIC_SANITY_API_VERSION,
	useCdn: true
});

const builder = imageUrlBuilder(client);

export function urlFor(source: any) {
	return builder.image(source);
}
```

---

## Common Schemas

### Homepage Schema

Create `sanity/schemas/homepage.ts`:

```typescript
import { defineType, defineField } from 'sanity';

export const homepage = defineType({
	name: 'homepage',
	title: 'Homepage',
	type: 'document',
	fields: [
		defineField({
			name: 'heroHeadline',
			title: 'Hero Headline',
			type: 'string',
			description: 'The main headline at the top of the page'
		}),
		defineField({
			name: 'heroSubheadline',
			title: 'Hero Subheadline',
			type: 'text',
			rows: 2,
			description: 'The smaller text below the headline'
		}),
		defineField({
			name: 'heroCtaText',
			title: 'Button Text',
			type: 'string',
			description: 'Text on the main call-to-action button'
		}),
		defineField({
			name: 'heroCtaLink',
			title: 'Button Link',
			type: 'string',
			description: 'Where the button goes when clicked'
		}),
		defineField({
			name: 'heroImage',
			title: 'Hero Image',
			type: 'image',
			options: { hotspot: true }
		})
	],
	preview: {
		prepare() {
			return { title: 'Homepage' };
		}
	}
});
```

### Register Schemas

Update `sanity/schema.ts`:

```typescript
import { type SchemaTypeDefinition } from 'sanity';
import { homepage } from './schemas/homepage';

export const schema: { types: SchemaTypeDefinition[] } = {
	types: [homepage]
};
```

---

## Fetching Content in SvelteKit

> **Important: Static Site Compatibility**
>
> This project uses `adapter-static` for static site generation. Server-side load functions (`+page.server.ts`) require either:
>
> 1. **Prerendering** (recommended): Add `export const prerender = true` to generate pages at build time
> 2. **Client-side fetching**: Use `+page.ts` or fetch in the component with `browser` check
>
> For most marketing sites, prerendering is the best approach—content is fetched at build time and pages are static.

### Option 1: Prerendered (Recommended for Static Sites)

```typescript
// src/routes/+page.server.ts
import { client } from '$lib/sanity';

// This tells SvelteKit to fetch data at BUILD time, not runtime
export const prerender = true;

export async function load() {
	const content = await client.fetch(`
    *[_type == "homepage"][0] {
      heroHeadline,
      heroSubheadline,
      heroCtaText,
      heroCtaLink,
      heroImage
    }
  `);

	return { content };
}
```

### Option 2: Client-Side Fetching (For Dynamic Content)

```typescript
// src/routes/+page.ts (note: NOT +page.server.ts)
import { browser } from '$app/environment';
import { client } from '$lib/sanity';

export async function load() {
	// Only fetch on client side for static builds
	if (!browser) return { content: null };

	const content = await client.fetch(`
    *[_type == "homepage"][0] {
      heroHeadline,
      heroSubheadline,
      heroCtaText,
      heroCtaLink,
      heroImage
    }
  `);

	return { content };
}
```

### Using in Components

```svelte
<!-- src/routes/+page.svelte -->
<script lang="ts">
	let { data } = $props();
	const { content } = data;
</script>

<section>
	<h1>{content?.heroHeadline || 'Welcome'}</h1>
	<p>{content?.heroSubheadline}</p>
</section>
```

### With Images

```svelte
<script lang="ts">
	import { urlFor } from '$lib/sanity';

	let { data } = $props();
	const { content } = data;
</script>

{#if content?.heroImage}
	<img src={urlFor(content.heroImage).width(1200).url()} alt="" />
{/if}
```

---

## Running Sanity Studio

### Local Development

```bash
cd sanity && npm run dev
```

Studio runs at `http://localhost:3333`

### Explain to User

> "Now you have an editing dashboard! Go to **localhost:3333** in your browser. You'll see your content types (Homepage, Features, Testimonials). Click on one to edit it, then publish your changes."

---

## Checklist for CMS Setup

- [ ] Sanity project initialized
- [ ] Environment variables set
- [ ] Sanity client created
- [ ] Schemas created for editable content
- [ ] Pages updated to fetch from Sanity
- [ ] **Prerender enabled** for static site compatibility (`export const prerender = true`)
- [ ] Fallback values for empty content
- [ ] Studio accessible at localhost:3333
- [ ] User understands how to edit content
- [ ] User understands they need to rebuild site after content changes (for prerendered pages)

## Explaining to Non-Technical Users

After setup, explain:

> "Here's how to update your website now:
>
> 1. Go to [Studio URL] and log in
> 2. Click on 'Homepage' (or whatever you want to edit)
> 3. Change the text or images
> 4. Click 'Publish' in the bottom right
> 5. Your website updates automatically!
>
> You can't break anything—worst case, just undo your changes."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/msjoshlopez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
