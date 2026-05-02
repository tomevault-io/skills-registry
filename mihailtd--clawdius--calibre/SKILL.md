---
name: calibre-ebook-library-management
description: Use Calibre CLI tools (calibredb, fetch-ebook-metadata) to search, fix, and improve your eBook library metadata Use when this capability is needed.
metadata:
  author: mihailtd
---

# Calibre eBook Library Management

This skill enables you to manage a Calibre eBook library via CLI tools. You can search for books, fetch metadata from online sources, fix missing/incorrect metadata, and maintain library health.

## Available CLI Tools

### 1. `fetch-ebook-metadata` - Find Metadata Online

Fetches book metadata from online sources (Google, Amazon, Open Library, etc.).

```bash
fetch-ebook-metadata [options]
```

**Key Options:**
- `-t "Title"` - Book title (use quotes for spaces)
- `-a "Author Name"` - Author name
- `-i ISBN` - ISBN number (most reliable)
- `-I identifier:value` - Other identifiers (asin, goodreads, etc.)
- `-p PLUGIN` - Limit to specific source: `Google`, `Amazon.com`, `Open Library`, `Big Book Search`, `Edelweiss`, `Google Images`
- `-c /path/to/cover.jpg` - Save cover image to file
- `-o` - Output as OPF XML (useful for piping to set_metadata)
- `-v` - Verbose output (shows search progress)
- `-d SECONDS` - Timeout (default: 30)

**Examples:**
```bash
# By title and author
fetch-ebook-metadata -t "The Great Gatsby" -a "F. Scott Fitzgerald"

# By ISBN (most accurate)
fetch-ebook-metadata -i 9780743273565

# With cover download
fetch-ebook-metadata -t "Dune" -a "Frank Herbert" -c cover.jpg

# As OPF for import
fetch-ebook-metadata -t "1984" -a "George Orwell" -o > metadata.opf
```

### 2. `calibredb` - Library Database Management

Manage the Calibre database: list, search, add, remove, update metadata.

**Global Options (apply to all commands):**
- `--library-path /path/to/library` - Specify library location
- `--with-library URL` - Connect to Content server (e.g., `http://localhost:8080/#library_id`)

#### Core Commands

**`calibredb list`** - List books in the library
```bash
calibredb list                                    # List all books
calibredb list -f title,authors,isbn,formats     # Specific fields
calibredb list -s "author:asimov"                # Search filter
calibredb list --for-machine                     # JSON output
calibredb list --sort-by title                   # Sort results
calibredb list -s "isbn:false"                   # Find books without ISBN
```

**`calibredb search`** - Search and return IDs
```bash
calibredb search "title:gatsby"                  # Returns: 42
calibredb search "author:asimov 'series:foundation'"  # Returns: 1,5,12,23
calibredb search "formats:epub and not formats:mobi"  # Format queries
calibredb search "date:>2020"                    # By date
calibredb search "rating:>=4"                    # By rating
```

**`calibredb show_metadata ID`** - View book metadata
```bash
calibredb show_metadata 42                       # Human readable
calibredb show_metadata 42 --as-opf              # As OPF XML
```

**`calibredb set_metadata ID [opf_file]`** - Update metadata
```bash
# From OPF file
calibredb set_metadata 42 metadata.opf

# Individual fields
calibredb set_metadata 42 --field "title:Correct Title"
calibredb set_metadata 42 --field "authors:First Last"
calibredb set_metadata 42 --field "isbn:9780743273565"
calibredb set_metadata 42 --field "tags:fiction,classic,must-read"
calibredb set_metadata 42 --field "series:Foundation" --field "series_index:1"
calibredb set_metadata 42 --field "identifiers:isbn:123,asin:B00ABC,goodreads:456"

# List available fields
calibredb set_metadata --list-fields
```

**`calibredb add`** - Add books
```bash
calibredb add book.epub                          # Add single book
calibredb add book.epub -t "Title" -a "Author"   # With metadata
calibredb add -r /path/to/folder                 # Recursively add folder
calibredb add -e                                 # Add empty book record
calibredb add book.epub -m ignore                # Auto-merge duplicates
```

**`calibredb remove IDs`** - Remove books
```bash
calibredb remove 42                              # Remove book 42
calibredb remove 1,5,10-15                       # Remove multiple (ranges work)
calibredb remove 42 --permanent                  # Skip recycle bin
```

**`calibredb add_format ID file`** - Add format to existing book
```bash
calibredb add_format 42 book.mobi
```

**`calibredb export IDs`** - Export books
```bash
calibredb export 42 --to-dir /exports
calibredb export 1,2,3 --formats epub,mobi --to-dir /exports
```

#### Library Health Commands

**`calibredb check_library`** - Find problems
```bash
calibredb check_library                          # All checks
calibredb check_library -r missing_covers        # Just missing covers
calibredb check_library -r missing_formats       # Missing format files
calibredb check_library --csv                    # CSV output for processing
```

Reports: `invalid_titles`, `extra_titles`, `invalid_authors`, `extra_authors`, `missing_formats`, `extra_formats`, `extra_files`, `missing_covers`, `extra_covers`, `malformed_formats`, `malformed_paths`, `failed_folders`

**`calibredb backup_metadata`** - Backup to OPF
```bash
calibredb backup_metadata                        # Out-of-date only
calibredb backup_metadata --all                  # All books
```

**`calibredb embed_metadata ID`** - Write metadata into book files
```bash
calibredb embed_metadata 42                      # Specific book
calibredb embed_metadata all                     # All books
calibredb embed_metadata 1 2 10-15               # Multiple/ranges
```

#### Full-Text Search

```bash
calibredb fts_index status                       # Check indexing status
calibredb fts_index enable                       # Enable FTS
calibredb fts_search "search terms"              # Search book contents
calibredb fts_search "term" --include-snippets   # With context
```

## Metadata Fetching Strategies

### Strategy 1: ISBN-First (Most Reliable)

If you have ISBN:
```bash
# Get metadata by ISBN
fetch-ebook-metadata -i 9780743273565 -o > /tmp/meta.opf

# Apply to book
calibredb set_metadata BOOK_ID /tmp/meta.opf
```

### Strategy 2: Title/Author Search with Cleanup

Titles often need cleaning for successful searches. Try multiple variations:

**Title Cleaning Steps:**
1. Remove subtitles (text after `:` or `-`)
2. Remove edition info ("2nd Edition", "Revised", etc.)
3. Remove series info in parentheses/brackets
4. Try first few significant words only
5. Remove articles ("The", "A", "An") from start

**Author Cleaning Steps:**
1. Try "FirstName LastName" format
2. Try last name only
3. Remove titles (Dr., Prof., etc.)
4. Remove Jr., Sr., III, etc.
5. Try main/first author only for multiple authors

**Example Workflow:**
```bash
# Original: "The Great Gatsby: A Novel (Classic Edition) [Annotated]"
# Try progressively simpler versions:

fetch-ebook-metadata -t "The Great Gatsby: A Novel" -a "F. Scott Fitzgerald" -v
fetch-ebook-metadata -t "The Great Gatsby" -a "F. Scott Fitzgerald" -v
fetch-ebook-metadata -t "Great Gatsby" -a "Fitzgerald" -v

# If still no results, try different sources
fetch-ebook-metadata -t "Great Gatsby" -a "Fitzgerald" -p "Open Library" -v
fetch-ebook-metadata -t "Great Gatsby" -a "Fitzgerald" -p "Google" -v
```

### Strategy 3: ASIN/Goodreads Lookup

If you have Amazon or Goodreads IDs:
```bash
fetch-ebook-metadata -I asin:B0BXN2D4SH -v
fetch-ebook-metadata -I goodreads:12345678 -v
```

### Strategy 4: Bulk Processing

For processing multiple books:
```bash
# Find books missing ISBN
IDS=$(calibredb search "isbn:false")

# Loop through (example in PowerShell)
foreach ($id in (calibredb search "isbn:false" -split ',')) {
    $meta = calibredb show_metadata $id
    # Extract title/author, search, update...
}
```

## Common Workflows

### Workflow 1: Fix Books with Missing Metadata

```bash
# 1. Find books with problems
calibredb list -s "isbn:false" -f id,title,authors

# 2. For each book, get current info
calibredb show_metadata BOOK_ID

# 3. Search online with cleaned title/author
fetch-ebook-metadata -t "Clean Title" -a "Author" -o > /tmp/meta.opf

# 4. Review and apply
cat /tmp/meta.opf
calibredb set_metadata BOOK_ID /tmp/meta.opf

# 5. Or set individual fields
calibredb set_metadata BOOK_ID --field "isbn:9780123456789"
```

### Workflow 2: Download Missing Covers

```bash
# 1. Find books without covers
calibredb check_library -r missing_covers

# 2. For each, fetch cover
fetch-ebook-metadata -t "Title" -a "Author" -c /tmp/cover.jpg

# 3. Apply cover (add as format, Calibre recognizes cover.jpg)
# Or use Calibre GUI/add_format if cover setting is needed
```

### Workflow 3: Clean Up Duplicate Authors

Common issue: "Asimov, Isaac" vs "Isaac Asimov" vs "asimov, isaac"

```bash
# Find books by variant author name
calibredb search "author:\"Asimov, Isaac\""

# Update to consistent format
calibredb set_metadata ID --field "authors:Isaac Asimov"
```

### Workflow 4: Add Series Information

```bash
# Set series for a book
calibredb set_metadata 42 --field "series:Foundation" --field "series_index:1"

# Find books that might be in a series (by author)
calibredb search "author:asimov"
calibredb list -s "author:asimov" -f id,title,series,series_index
```

### Workflow 5: Health Check and Repair

```bash
# Full library check
calibredb check_library

# Check specific issues
calibredb check_library -r missing_formats,missing_covers,malformed_paths

# Backup all metadata
calibredb backup_metadata --all

# Re-embed metadata into files
calibredb embed_metadata all
```

## Search Query Syntax

Calibre uses a powerful search syntax:

| Query | Meaning |
|-------|---------|
| `title:word` | Title contains "word" |
| `title:"exact phrase"` | Title contains exact phrase |
| `author:name` | Author contains "name" |
| `series:name` | In series containing "name" |
| `tags:fiction` | Has tag "fiction" |
| `formats:epub` | Has EPUB format |
| `rating:>=4` | Rating 4+ stars |
| `date:>2020` | Added after 2020 |
| `isbn:true` / `isbn:false` | Has/missing ISBN |
| `cover:true` / `cover:false` | Has/missing cover |
| `not QUERY` | Negation |
| `QUERY1 and QUERY2` | Both conditions |
| `QUERY1 or QUERY2` | Either condition |

**Examples:**
```bash
calibredb search "author:asimov series:foundation"
calibredb search "formats:epub and not formats:mobi"
calibredb search "isbn:false and author:known"
calibredb search "tags:fiction rating:>=4 date:>2020"
```

## Available Metadata Fields

**Standard fields for `--field`:**
- `title` - Book title
- `authors` - Comma-separated author names
- `author_sort` - Sort key for author
- `publisher` - Publisher name
- `pubdate` - Publication date
- `series` - Series name
- `series_index` - Position in series (decimal)
- `rating` - 0-10 (displayed as 0-5 stars)
- `tags` - Comma-separated tags
- `comments` - HTML description
- `isbn` - ISBN-10 or ISBN-13
- `identifiers` - Format: `type:value,type:value` (isbn, asin, goodreads, amazon, google, etc.)
- `languages` - ISO639 codes (en, fr, de, etc.)

**List custom columns:**
```bash
calibredb custom_columns -d
```

## Tips for Shell Tool Usage

When using these commands via the `shell_run` tool:

1. **Always quote arguments with spaces:**
   ```bash
   fetch-ebook-metadata -t "The Great Gatsby" -a "F. Scott Fitzgerald"
   ```

2. **Use --for-machine for parsing:**
   ```bash
   calibredb list -s "author:asimov" --for-machine
   ```

3. **Capture OPF output:**
   ```bash
   fetch-ebook-metadata -t "Title" -a "Author" -o > /tmp/metadata.opf
   ```

4. **Check exit codes and use -v for debugging:**
   ```bash
   fetch-ebook-metadata -t "Title" -v  # Shows search progress
   ```

5. **Work in batches for bulk operations** - Don't try to process hundreds at once

6. **Verify changes:**
   ```bash
   calibredb show_metadata BOOK_ID  # After updating
   ```

## Error Handling

**"No results found"** for fetch-ebook-metadata:
- Clean up title (remove subtitles, edition info)
- Try author last name only
- Try different plugin (-p Google, -p "Open Library")
- Check for typos
- Some books simply aren't indexed online

**"Library locked"** for calibredb:
- Close Calibre GUI
- Check for other calibredb processes
- Library may be on network drive with lock issues

**Wrong book matched:**
- Always verify fetched metadata before applying
- Use ISBN when possible
- Check publication year matches

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mihailtd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
