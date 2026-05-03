---
name: turath-research-skill
description: This skill provides specialized tools for searching and retrieving Islamic classical texts from the Turath.io API and local metadata database. Use this skill when researching Islamic literature, finding book references, extracting page content, or creating content based on classical Islamic texts. Use when this capability is needed.
metadata:
  author: herbras
---

# Turath Research Skill

This skill provides direct access to the Turath.io library (100,000+ Islamic classical texts) and local metadata database for deep research into Islamic literature.

## When to Use

This skill should be used when:
- Searching for specific Arabic terms across Islamic classical texts
- Retrieving book details, author biographies, or page content
- Finding category or author IDs for filtered searches
- Creating content based on classical Islamic texts (articles, videos, social media)
- Building research workflows for Islamic studies

## Available Functions

Import from `src/turath_skill/scripts/turath_tools.py`:

```python
from src.turath_skill.scripts.turath_tools import (
    search_library,
    get_book_details,
    get_page_content,
    get_author_bio,
    get_filter_ids,
    list_all_categories,
    list_all_authors,
)
```

### 1. `search_library(q, precision=0, cat=None, author=None, ...)`

Search the library for Arabic queries. Returns results enriched with local metadata.

**Parameters:**
- `q` (str): Arabic search query
- `precision` (int): 0=broad, 3=exact match
- `cat` (str): Category IDs to filter (comma-separated)
- `author` (str): Author IDs to filter (comma-separated)

**Example:**
```python
results = await search_library(q="صحيح البخاري", precision=3)
# Returns: {"count": 99733, "data": [...]}
```

### 2. `get_book_details(book_id, include=None)`

Get detailed information about a specific book.

**Parameters:**
- `book_id` (int): The book ID
- `include` (str): Optional - "meta,index" for table of contents

**Example:**
```python
details = await get_book_details(book_id=9942, include="index")
# Returns book info with local enrichment (PDF links, author, category)
```

### 3. `get_page_content(book_id, pg)`

Get the text content of a specific page.

**Parameters:**
- `book_id` (int): The book ID
- `pg` (int): Page number

**Example:**
```python
page = await get_page_content(book_id=9942, pg=5)
# Returns: {"text": "...page content..."}
```

### 4. `get_author_bio(author_id)`

Get author biography and death dates.

**Example:**
```python
bio = await get_author_bio(author_id=123)
```

### 5. `get_filter_ids(category_name=None, author_name=None)`

Find category/author IDs by Arabic name for filtering searches.

**Example:**
```python
ids = await get_filter_ids(category_name="فقه")
# Returns: {"category_ids": "11,17,14,16,18,15,12,19"}
```

### 6. `list_all_categories()`

List all 40 available categories.

**Example:**
```python
cats = await list_all_categories()
# Returns: {"categories": [{"id": 11, "name": "أصول الفقه"}, ...]}
```

### 7. `list_all_authors()`

List all 3,102 available authors.

**Example:**
```python
authors = await list_all_authors()
# Returns: {"authors": [{"id": 1, "name": "...", "death": 123}, ...]}
```

## Workflow Examples

### Research Workflow

```python
# 1. Find category ID
ids = await get_filter_ids(category_name="الفقه الشافعي")

# 2. Search with filter
results = await search_library(q="الصلاة", cat=ids["category_ids"])

# 3. Get book details
book = await get_book_details(book_id=results["data"][0]["book_id"])

# 4. Extract page content
page = await get_page_content(book_id=book["id"], pg=1)
```

### Content Creation Workflow

```python
# Get book structure
details = await get_book_details(book_id=9942, include="index")

# Extract content for each topic
for pg in range(1, 10):
    content = await get_page_content(book_id=9942, pg=pg)
    # Process content for articles/videos/social media
```

## Local Database

The skill uses a local SQLite database (`data/turath_metadata.db`) containing:
- 40 categories
- 3,102 authors
- Book metadata with PDF links

This enables offline metadata access and enriches API results with additional information.

## Test Scripts

Available test scripts in `_test_/`:
- `test_new_functional_tools.py` - Core function tests
- `test_simple_islamic.py` - Islamic query workflow test
- `test_agent_tools.py` - All available tools test
- `content_generator_ushul.py` - Content generation from Ushul book

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/herbras) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
