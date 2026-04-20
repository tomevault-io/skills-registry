---
name: tmdb-data
description: TMDB API integration specialist for fetching movies, TV shows, and managing TMDB data Use when this capability is needed.
metadata:
  author: marthaya-putra
---

# TMDB Data Specialist

## Instructions
When working with TMDB API integration:

1. **Data Fetching**
   - Use functions from `src/lib/data/trending.ts` for trending content
   - Use `src/lib/data/discover.ts` for content discovery
   - Use `src/lib/data/search.ts` for multi-search functionality
   - Always handle API errors gracefully

2. **Data Transformation**
   - Use `convertToDiscoverResult()` to standardize TMDB responses
   - Extract release years with `getReleasedYear()`
   - Map genre IDs using the genre mapping utilities
   - Differentiate between movie and TV show content types

3. **API Configuration**
   - Check `INCLUDE_ADULT_CONTENT` environment variable
   - Implement proper error handling for rate limits
   - Use the base URL configuration from providers
   - Handle image URLs with proper sizing

## Examples

**Fetching trending movies:**
```typescript
import { getTrendingMovies } from '@/lib/data/trending'

const trending = await getTrendingMovies()
// Returns array of trending movies with metadata
```

**Searching for content:**
```typescript
import { multiSearch } from '@/lib/data/search'

const results = await multiSearch('query')
// Returns results across movies, TV shows, and people
```

**Converting TMDB response:**
```typescript
import { convertToDiscoverResult } from '@/lib/data/search'

const formatted = convertToDiscoverResult(tmdbResponse)
// Standardizes response format for UI
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marthaya-putra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
