---
name: markdown-structure-to-yaml
description: Extract markdown document structure (headings, code blocks, lists) as YAML metadata - for token-efficient grep-friendly analysis Use when this capability is needed.
metadata:
  author: glenn-syj
---

# Markdown Structure to YAML

## Purpose

Extract markdown document structure as nested YAML with line numbers:
- Headings (h1, h2, h3, ...) with nested hierarchy
- Code blocks with language
- Lists with counts
- Line numbers for quick reference

## Why

Long markdown files are hard to grep. Convert structure to nested YAML for quick navigation:
- Find all h2: `python script.py file.md | grep 'h2:'`
- Find code blocks: `python script.py file.md | grep 'code:'`
- Quick stats: `python script.py file.md --stat`

## Script Usage

```bash
python markdown-structure-to-yaml.py <markdown-file>
python markdown-structure-to-yaml.py <markdown-file> --stat   # Show stats only
```

## Output Format

### Nested YAML with Line Numbers
```yaml
- h1: Document Title @ 1
  - h2: Section 1 @ 5
    - h3: Subsection 1 @ 10
      - code: bash @ 12
      - list: unordered (5 items) @ 15
  - h2: Section 2 @ 20
```

### Statistics
```bash
$ python script.py file.md --stat
total_h1: 1
total_h2: 2
total_h3: 5
total_code_blocks: 3
total_lists: 7
```

## Example

### Input (sample.md)
```markdown
# My Skill

## Usage

Run this command:

```bash
echo "hello"

## Examples

- item 1
- item 2
```

### Output
```yaml
- h1: My Skill @ 1
  - h2: Usage @ 3
    - code: bash @ 6
  - h2: Examples @ 10
    - list: unordered (2 items) @ 12
```

## Grep Patterns

```bash
# Extract all headings with line numbers
python script.py file.md | grep 'h1:'
python script.py file.md | grep 'h2:'
python script.py file.md | grep 'h3:'

# Find code blocks with line numbers
python script.py file.md | grep 'code:'

# Find lists
python script.py file.md | grep 'list:'

# Read specific section by line number
# e.g., read line 12: READ file.md offset 12 limit 10
```

## Dependencies

- `python` or `python3`
- `mistune` (markdown parser)
- `pyyaml` (for YAML output)

### Install
```bash
pip install mistune pyyaml
```

## Notes

- Frontmatter (--- delimited) is automatically removed
- Uses `mistune` for accurate markdown parsing
- Nested structure reflects actual document hierarchy
- Line numbers enable quick reference to original markdown
- Code blocks without language default to `text`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/glenn-syj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
