---
name: book-logger
description: Batch import books user has already read (or abandoned) into Notion database. Triggers on "I've read...", "I finished...", "books I've read", multiple books/series in one message, or sentiment language about past reading (loved, liked, etc.). Does NOT enrich with prices or availability - use book-tracker skill for that. Use when this capability is needed.
metadata:
  author: scarabone
---

# Book Logger

Batch import read/abandoned books into the Notion Books database with ratings based on user sentiment.

## Workflow

### Step 1: Find Database

Never hardcode database IDs. Always:
1. Search Notion for "Books" database
2. Fetch to get current data source ID and schema
3. Confirm expected fields exist before writing

### Step 2: Gather Book Info

Web search for series/book details:
- Series: "[Series name] book series [author]" → titles, years, book count, author
- Standalone: "[Title] [Author] book year published"

Skip novellas and short stories unless user specifically requests them.

### Step 3: Map User Sentiment

**Status:**
| User says | Status |
|-----------|--------|
| read, finished, loved, liked (default) | Finished |
| abandoned, DNF, couldn't finish, gave up | Abandoned |

**Rating:**
| Sentiment | Rating |
|-----------|--------|
| loved, LOVED, favorite, amazing | ⭐⭐⭐⭐⭐ |
| really liked, really enjoyed, great | ⭐⭐⭐⭐ |
| liked, good, enjoyed | ⭐⭐⭐⭐ |
| okay, fine, decent, meh | ⭐⭐⭐ |
| didn't like, disappointing | ⭐⭐ |
| hated, terrible | ⭐ |

**Abandoned books:** Rate only when user gives sentiment or reason that signals preferences (e.g., "too slow" → ⭐⭐). No rating if abandoned for life reasons or no context given.

### Step 4: Batch Create Pages

Create pages with:
- Title
- Author (use pen name if applicable, e.g., "James S.A. Corey")
- Year Published (original publication year)
- Genre (1-2 max from: Fiction, Non-Fiction, Mystery, Thriller, Sci-Fi, Fantasy, Historical, Biography, Memoir, Self-Help)
- Status (Finished or Abandoned)
- My Rating (mapped from sentiment)
- Series (if applicable)
- Series Number (if applicable)

Do NOT populate: Amazon Link, Amazon Rating, Goodreads Link, Goodreads Rating, Kindle Price, Format, Notes, AFPLS fields.

Do NOT check for duplicates - just add books.

### Step 5: Confirm

Brief summary grouped by series:
```
Added 9 Expanse books (⭐⭐⭐⭐⭐)
Added 3 Wayward Pines books (⭐⭐⭐⭐⭐)
Added 4 Nick Hall books (⭐⭐⭐⭐)
```

Do not itemize individual books unless batch is small (<5 total).

## Input Patterns

| User input | Action |
|------------|--------|
| "I read The Martian, loved it" | Single book, Finished, ⭐⭐⭐⭐⭐ |
| "I read all 9 Expanse books" | Search series, add all 9 |
| "Read the Thursday Murder Club series" | Search for book count, add all |
| "Expanse (loved), Nick Hall (liked)" | Multiple series, different ratings |
| "Tried Dune but couldn't finish - too slow" | Abandoned, ⭐⭐ (preference signal) |
| "Never got back to it" | Abandoned, no rating |

## Rules

**Always:**
- Search Notion fresh for database ID
- Web search for accurate book/series info
- Use original publication years
- Keep confirmations brief

**Never:**
- Hardcode database or data source IDs
- Check for duplicates
- Add novellas/short stories (unless requested)
- Itemize large batches
- Ask for confirmation before adding
- Look up prices, ratings, or availability (that's book-tracker)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scarabone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
