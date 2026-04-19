---
name: split
description: Read documents and split them into chunks Use when this capability is needed.
metadata:
  author: jbouder
---

# Split Skill

Read documents and split them into manageable chunks for processing, analysis, or transfer. Like a master breaking boards, this skill shatters overwhelming information into fragments that can be easily mastered.

## Capabilities

- **Split by Lines**: Split files into chunks of a precise number of lines.
- **Split by Size**: Split files into chunks of an approximate character count.
- **Split by Sections**: Split at structural boundaries like markdown headers.
- **Split by Paragraphs**: Split at blank line delimiters.
- **Support Overlap**: Maintain context between chunks with an overlapping buffer.

## Usage Examples

**Split a file into standard chunks:**

```
/split README.md
```

**Split by precise line count:**

```
/split document.txt --lines 100
```

**Split by markdown sections:**

```
/split document.md --sections
```

**Split by paragraphs:**

```
/split article.txt --paragraphs
```

**Split with context overlap:**

```
/split document.txt --lines 100 --overlap 10
```

## Options

| Option         | Description                          | Default |
| -------------- | ------------------------------------ | ------- |
| `--lines N`    | Split every N lines                  | 100     |
| `--size N`     | Split at approximately N characters  | -       |
| `--sections`   | Split at markdown headers (# ## ###) | -       |
| `--paragraphs` | Split at blank lines                 | -       |
| `--overlap N`  | Include N lines of context           | 0       |

## Output Format

Present each chunk with a header showing its number and line range:

```
══════════════════════════════════════════════════════
CHUNK 1 of 5 (lines 1-100)
══════════════════════════════════════════════════════

[chunk content here]

══════════════════════════════════════════════════════
CHUNK 2 of 5 (lines 101-200)
══════════════════════════════════════════════════════

[chunk content here]
```

## Splitting Strategies

### By Lines (default)

Split at fixed line intervals. Best for uniform content like logs or data files.

### By Size

Split at approximate character counts, breaking at line boundaries. Best when targeting specific output sizes.

### By Sections

Split at markdown headers (lines starting with #). Best for documentation. Each section becomes its own chunk.

### By Paragraphs

Split at blank lines. Best for prose where paragraphs are the logical units.

## Workflow

1. **Read the file** using the Read tool.
2. **Get file stats** using `wc -l` to show total lines.
3. **Apply splitting strategy** based on options.
4. **Display chunks** with headers and summaries.
5. **Show summary** with total chunks and average chunk size.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jbouder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
