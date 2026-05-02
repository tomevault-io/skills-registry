---
name: brain-surgery
description: Externalize Claude's auto-memory into your project directory so it's git-committable and survives across machines Use when this capability is needed.
metadata:
  author: ellyseum
---

# /brain-surgery

Externalizes Claude's auto-memory into `.context/memory/` in the project root, replacing the auto-memory directory with a symlink. This makes Claude's memory git-trackable, portable, and collaboratively editable.

## Usage

- `/brain-surgery` — Set up the symlink (safe, non-destructive)
- `/brain-surgery --undo` — Restore the default auto-memory directory

## Instructions

### Step 1: Detect project root

Use `$PWD` as the project root.

**Safety check:** If `$PWD` is `$HOME`, `/home`, `/tmp`, `/`, or any path with fewer than 3 components, **STOP** and warn the user:
> "This doesn't look like a project directory. Run this from inside a project root."

### Step 2: Compute the auto-memory path

Claude's auto-memory lives at:
```
~/.claude/projects/<encoded-path>/memory/
```

The encoded path replaces all `/` with `-`, including the leading slash. So `/home/jocel/projects/foo` becomes `-home-jocel-projects-foo`.

Compute it:
```bash
encoded="$(echo "$PWD" | tr '/' '-')"
auto_memory="$HOME/.claude/projects/${encoded}/memory"
```

### Step 3: Determine the operation

Check if the user passed `--undo`. If so, skip to the **Undo** section below.

### Step 4: Check current state

```bash
ls -la "$auto_memory" 2>/dev/null
```

- **If it's already a symlink pointing to `.context/memory/`** → Tell the user "Already set up!" and show the symlink. Done.
- **If it's a real directory** → Proceed to Step 5 (merge existing files).
- **If it doesn't exist** → Skip to Step 6 (create fresh).

### Step 5: Merge existing auto-memory files

This is the delicate part. Existing memory files must be preserved.

1. **Back up first:**
   ```bash
   backup="/tmp/claude-memory-backup-$(date +%s)"
   cp -a "$auto_memory" "$backup"
   ```

2. **Create `.context/memory/` if needed:**
   ```bash
   mkdir -p .context/memory
   ```

3. **Copy files (no-clobber — don't overwrite existing `.context/memory/` files):**
   ```bash
   cp -n "$auto_memory"/* .context/memory/ 2>/dev/null
   ```

4. **Verify the merge:**
   ```bash
   src_count=$(ls -1 "$auto_memory" 2>/dev/null | wc -l)
   dst_count=$(ls -1 .context/memory 2>/dev/null | wc -l)
   ```
   The destination count should be >= the source count. If `dst_count < src_count`, **ABORT** and tell the user:
   > "Merge verification failed. Your backup is at: $backup"

5. **Remove the original directory:**
   ```bash
   rm -rf "$auto_memory"
   ```

6. Tell the user how many files were merged and that the backup is at `$backup`.

### Step 6: Create the symlink

```bash
mkdir -p "$(dirname "$auto_memory")"
mkdir -p .context/memory
ln -s "$PWD/.context/memory" "$auto_memory"
```

Verify it worked:
```bash
readlink "$auto_memory"
```

### Step 7: Create starter MEMORY.md

Only if `.context/memory/MEMORY.md` doesn't already exist:

```markdown
# Project Memory

> Auto-memory externalized by claude-brain-surgery.
> This file is loaded into Claude's system prompt at the start of every session.
> Keep it under 200 lines — overflow gets truncated.
```

### Step 8: Report results

Tell the user:
- What was linked (`$auto_memory` → `.context/memory/`)
- How many files were merged (if any)
- Remind them to `git add .context/memory/` to start tracking it
- Mention that `.context/` is a good place for other project context files too

---

## Undo

When `--undo` is passed:

### Step 1: Check current state

```bash
readlink "$auto_memory"
```

- **If it's not a symlink** → Tell the user "Nothing to undo — auto-memory is already a regular directory."
- **If it's a symlink** → Proceed.

### Step 2: Copy files back

```bash
target="$(readlink "$auto_memory")"
rm "$auto_memory"
mkdir -p "$auto_memory"
cp -a "$target"/* "$auto_memory/" 2>/dev/null
```

### Step 3: Report

Tell the user:
- Files were copied back to `$auto_memory`
- The symlink was removed
- `.context/memory/` was left intact (they can delete it manually if they want)
- Claude will now use the regular auto-memory directory again

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ellyseum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
