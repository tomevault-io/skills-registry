---
name: using-modern-cli
description: >- Use when this capability is needed.
metadata:
  author: neversight
---

# Modern CLI Tools

Use faster, ergonomic command-line tools installed on this system.

## Quick Reference

| Task         | Modern  | Traditional | Why Modern                          |
| ------------ | ------- | ----------- | ----------------------------------- |
| Search text  | `rg`    | grep        | 10-100x faster, respects .gitignore |
| Find files   | `fd`    | find        | Simpler syntax, ignores .git        |
| View files   | `bat`   | cat         | Syntax highlighting, line numbers   |
| List files   | `eza`   | ls          | Icons, git status, tree view        |
| Replace text | `sd`    | sed         | Intuitive regex, preview mode       |
| Disk usage   | `dust`  | du          | Visual tree, sorted by size         |
| Processes    | `procs` | ps          | Tree view, sortable columns         |
| Diff files   | `delta` | diff        | Syntax highlighting, side-by-side   |

## Examples

```bash
# Search: rg instead of grep
rg "TODO" --type go           # Search Go files
rg -A 3 "error"               # 3 lines after match
rg -l "import"                # List files only

# Find: fd instead of find
fd "\.go$"                    # Find Go files
fd -e json src/               # By extension in src/
fd -x wc -l {}                # Execute on matches

# View: bat instead of cat
bat main.go                   # With syntax highlighting
bat -n file.py                # Line numbers only

# List: eza instead of ls
eza -la --git                 # Long format with git status
eza --tree -L 2               # Tree view, 2 levels

# Replace: sd instead of sed
sd "old" "new" file.txt       # Simple replacement
sd -p "pattern" "new"         # Preview changes first

# Disk: dust instead of du
dust -d 2                     # 2 levels deep

# Processes: procs instead of ps
procs --tree                  # Process tree
```

## Productivity Tools

```bash
# Fuzzy finder (pipe with fd/rg)
vim $(fd . | fzf)
rg "pattern" | fzf

# Benchmarking
hyperfine "rg pattern" "grep -r pattern"

# Code statistics
tokei .

# Markdown preview
glow README.md

# JSON/YAML processing
jq '.key' file.json
yq '.key' file.yaml
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
