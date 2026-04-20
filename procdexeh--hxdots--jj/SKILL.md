---
name: jj
description: Jujutsu (jj) version control patterns, commands, revsets, and workflows for efficient development Use when this capability is needed.
metadata:
  author: procdexeh
---

# Jujutsu (jj) Version Control

The spice must flow. Your commits must evolve.

## Core Paradigm

**jj is NOT git with different commands. It's a different mental model.**

| Git | jj |
|-----|-----|
| Working directory + staging + commits | Working copy IS a commit |
| Branches point to commits | Bookmarks point to commits (optional) |
| Commit SHA changes on rewrite | Change ID persists through rewrites |
| Manual staging required | Auto-tracks all changes |
| Checkout detaches HEAD | `edit` makes commit mutable |

## VCS Detection

**ALWAYS check for `.jj/` before ANY git command - use jj if present.**

## Essential Commands

### Basic Operations
```
jj new [-m "message"]          # Create new change (child of @)
jj new REV [-m "message"]      # Create child of REV
jj new -B REV [-m "message"]   # Create change BEFORE REV
jj describe -m "message"       # Set message for @
jj squash [-i]                 # Move @ changes into parent
jj squash --into REV           # Squash @ into specific commit
jj split                       # Split @ interactively
jj abandon                     # Remove @ (parent inherits content)
```

### Inspection
```
jj diff [-r REV]               # Show changes (default: @)
jj show REV                    # Full commit details with diff
jj status                      # Working copy file status
jj log [-r REVSET]             # Show commit graph
```

### Navigation
```
jj edit REV                    # Make REV the working copy
jj next [--edit]               # Go to child commit
jj prev [--edit]               # Go to parent commit
```

### Stack Management
```
jj rebase -d DEST              # Rebase @ onto DEST
jj rebase -s SRC -d DEST       # Rebase SRC subtree onto DEST
jj rebase -r REV -d DEST       # Rebase single REV (not descendants)
jj log -r "stack()"            # View current stack
```

### Bookmarks (not branches!)
```
jj bookmark create NAME        # Create at @
jj bookmark move NAME --to REV # Move bookmark
jj bookmark delete NAME        # Remove bookmark
jj bookmark list               # Show all bookmarks
jj bookmark track NAME@REMOTE  # Track remote bookmark
```

### Git Interop
```
jj git fetch [--remote R]      # Fetch from git remote
jj git push [--bookmark B]     # Push bookmark to git
jj git push --all              # Push all tracked bookmarks
jj git push --deleted          # Delete remote bookmarks
```

### Advanced
```
jj absorb                      # Auto-squash changes to relevant commits
jj diffedit -r REV             # Edit REV contents in editor
jj resolve --tool TOOL         # Resolve conflicts with merge tool
jj evolog [-r REV]             # Evolution log (how commit changed)
jj op log                      # Show operation history
jj undo                        # Undo last operation
jj op restore OP_ID            # Restore to specific operation
jj fix                         # Run configured formatters/linters
jj parallelize REVS            # Make linear commits parallel
jj workspace add PATH          # Multiple working copies
```

## Revset Language

### Operators
```
x | y         # Union
x & y         # Intersection
x ~ y         # Difference (x minus y)
x::y          # DAG range (x to y inclusive)
::x           # Ancestors (inclusive)
x::           # Descendants (inclusive)
```

### Special Symbols
```
@             # Current working copy
@-            # Parent(s) of @
@+            # Children of @
root()        # Root commit
```

### Functions

| Function | Returns |
|----------|---------|
| `author(pattern)` | Commits by author |
| `mine()` | Your commits (config) |
| `empty()` | No-change commits |
| `conflicts()` | Commits with conflicts |
| `merges()` | Merge commits |
| `mutable()` | Editable commits |
| `immutable()` | Protected commits |
| `description(pattern)` | By commit message |
| `description(exact:"text")` | Exact message match |
| `description(glob:"wip:*")` | Glob message match |
| `file(pattern)` | Commits touching file |
| `heads(x)` | Head commits in set |
| `roots(x)` | Root commits in set |
| `ancestors(x[, depth])` | All ancestors |
| `descendants(x)` | All descendants |
| `reachable(x, domain)` | Reachable within domain |
| `bookmarks()` | Commits with bookmarks |
| `trunk()` | Main branch head |
| `latest(x, n)` | Latest n from set |

## User Config

### Aliases
```
bm  = bookmark
gf  = git fetch
gp  = git push
tug = advance bookmark to closest pushable commit
      jj tug           # closest bookmark → closest pushable
      jj tug <name>    # named bookmark → closest pushable
stack = log -r "stack()"
```

### Revset Aliases
```
mine()                    # author(procdexeh)
wip()                     # mine() ~ immutable()
open()                    # mine() ~ ::trunk()
closest_bookmark(to)      # heads(::to & bookmarks())
closest_pushable(to)      # heads(::to & mutable() & ~description(exact:"") & (~empty() | merges()))
current()                 # @:: & mutable()
stack()                   # reachable(@, mutable())
```

### Private Commits (push-blocked)
```
description(glob:'wip:*') | description(glob:'WIP:*') | description(exact:'')
```

### Signing
- `behavior = "own"` - only sign your commits
- `sign-on-push = true` - sign when pushing to git

## Workflow Patterns

### Squash Workflow (Recommended)
```bash
jj describe -m "feat: add new capability"   # Describe intent
# ... make changes ...
jj new                                       # Move to fresh change
# ... more changes ...
jj squash                                    # Fold into parent
```

### Edit Workflow
```bash
jj new -m "feat: refactor API"               # Create with message
# ... work ...
jj new -m "feat: add tests"                  # Next chunk
# ... work ...
jj prev --edit                               # Back to refactor
# ... fix ...
jj next --edit                               # Forward to tests
```

### Stack + Push
```bash
jj log -r "stack()"                          # View stack
jj bookmark create feature-x                 # Name it
# ... iterate on stack ...
jj tug feature-x                             # Advance bookmark
jj git push --bookmark feature-x             # Push
```

### Rebase onto trunk
```bash
jj git fetch                                 # Update trunk
jj rebase -d trunk()                         # Rebase stack
```

## Common Operations

| Task | Command |
|------|---------|
| Start feature | `jj new -m "feat: X"` |
| Save progress | Auto-saved (WC is a commit) |
| Set message | `jj describe -m "msg"` |
| Move to next | `jj new` |
| Amend current | Just edit files (already in @) |
| Fix earlier commit | `jj absorb` or `jj squash --into REV` |
| Split commit | `jj split` |
| Rebase stack | `jj rebase -d trunk()` |
| Push to git | `jj tug && jj gp` |
| Update from git | `jj gf` then `jj rebase -d trunk()` |
| View history | `jj log` or `jj log -r "mine()"` |
| Undo mistake | `jj undo` |
| Resolve conflicts | `jj resolve` or edit conflict markers |

## Anti-Patterns

| Don't | Why |
|-------|-----|
| Use `jj branch` | Deprecated, use `bookmark` |
| Forget WC is a commit | All changes auto-tracked, describe before `new` |
| Push empty descriptions | Blocked by private-commits revset |
| Run git commands in jj repo | Use `jj git` subcommands |
| Abandon without squashing | Unique changes lost |
| Edit immutable commits | Breaks trunk history |
| Think in git mental model | No staging, no detached HEAD, no branch switching |

---

*"The spice extends life. jj extends development flow."*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/procdexeh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
