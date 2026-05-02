---
name: codeledger
description: Track AI code contributions for auditing and attribution. Use this skill BEFORE writing, editing, creating, or modifying any code files. Enables automatic detection of AI-authored vs human-authored code changes. Required for code attribution compliance. Use when this capability is needed.
metadata:
  author: jaguarliuu
---

# CodeLedger - AI Code Attribution Tracking

This project uses CodeLedger to automatically track which code was written by AI and which was written by humans. This is **mandatory** for all code changes to ensure proper attribution and auditing.

## Bundled Binaries

This skill includes pre-built `ledger` binaries for all platforms in the `bin/` directory:

```
bin/
├── ledger-windows-amd64.exe   # Windows x64
├── ledger-windows-arm64.exe   # Windows ARM64
├── ledger-darwin-amd64        # macOS Intel
├── ledger-darwin-arm64        # macOS Apple Silicon (M1/M2/M3)
├── ledger-linux-amd64         # Linux x64
└── ledger-linux-arm64         # Linux ARM64
```

## How to Run ledger Commands

**IMPORTANT**: Use the bundled binary from this skill's `bin/` directory. Do NOT assume `ledger` is in PATH.

### Step 1: Detect Platform and Set Binary Path

First, detect the current platform and architecture to determine which binary to use:

```bash
# Detect OS and architecture
OS=$(uname -s | tr '[:upper:]' '[:lower:]')
ARCH=$(uname -m)

# Map to binary name
case "$OS" in
  darwin) OS_NAME="darwin" ;;
  linux)  OS_NAME="linux" ;;
  mingw*|msys*|cygwin*) OS_NAME="windows" ;;
  *) OS_NAME="linux" ;;  # default fallback
esac

case "$ARCH" in
  x86_64|amd64) ARCH_NAME="amd64" ;;
  arm64|aarch64) ARCH_NAME="arm64" ;;
  *) ARCH_NAME="amd64" ;;  # default fallback
esac

# Construct binary name
if [ "$OS_NAME" = "windows" ]; then
  LEDGER_BIN="ledger-${OS_NAME}-${ARCH_NAME}.exe"
else
  LEDGER_BIN="ledger-${OS_NAME}-${ARCH_NAME}"
fi

echo "Using binary: $LEDGER_BIN"
```

### Step 2: Locate the Skill Directory

The binary is located in this skill's `bin/` directory. Find the skill directory path:

```bash
# If skill is installed at project level: .claude/skills/codeledger/bin/
# If skill is installed at user level: ~/.claude/skills/codeledger/bin/

# Check project level first
if [ -d ".claude/skills/codeledger/bin" ]; then
  SKILL_BIN=".claude/skills/codeledger/bin"
elif [ -d "$HOME/.claude/skills/codeledger/bin" ]; then
  SKILL_BIN="$HOME/.claude/skills/codeledger/bin"
else
  echo "Error: CodeLedger skill bin directory not found"
  exit 1
fi

LEDGER="$SKILL_BIN/$LEDGER_BIN"

# Make executable (needed for Unix systems)
chmod +x "$LEDGER" 2>/dev/null || true
```

### Step 3: Run Commands

Now use `$LEDGER` to run any ledger command:

```bash
# Initialize (first time only)
"$LEDGER" init
"$LEDGER" install-hooks

# Before writing code
"$LEDGER" checkpoint pre

# After writing code
"$LEDGER" checkpoint post

# View stats
"$LEDGER" stats
```

## Complete One-Liner Setup

For convenience, here's a one-liner to set up the `LEDGER` variable:

```bash
# Detect and set LEDGER path (copy this entire block)
OS=$(uname -s | tr '[:upper:]' '[:lower:]'); ARCH=$(uname -m); case "$OS" in darwin) OS_NAME="darwin";; linux) OS_NAME="linux";; mingw*|msys*|cygwin*) OS_NAME="windows";; *) OS_NAME="linux";; esac; case "$ARCH" in x86_64|amd64) ARCH_NAME="amd64";; arm64|aarch64) ARCH_NAME="arm64";; *) ARCH_NAME="amd64";; esac; if [ "$OS_NAME" = "windows" ]; then LEDGER_BIN="ledger-${OS_NAME}-${ARCH_NAME}.exe"; else LEDGER_BIN="ledger-${OS_NAME}-${ARCH_NAME}"; fi; if [ -d ".claude/skills/codeledger/bin" ]; then LEDGER=".claude/skills/codeledger/bin/$LEDGER_BIN"; elif [ -d "$HOME/.claude/skills/codeledger/bin" ]; then LEDGER="$HOME/.claude/skills/codeledger/bin/$LEDGER_BIN"; fi; chmod +x "$LEDGER" 2>/dev/null || true; echo "LEDGER=$LEDGER"
```

---

## Why This Matters

CodeLedger records attribution data in Git notes, enabling:
- **Audit compliance**: Know exactly which code was AI-generated
- **Transparency**: Clear distinction between AI and human contributions
- **Statistics**: Track AI vs human code ratios over time

## Workflow Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    CodeLedger Workflow                       │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  1. BEFORE writing code    ──►  "$LEDGER" checkpoint pre     │
│                                   (records current state)    │
│                                                              │
│  2. Write/edit code        ──►  [your code changes]          │
│                                                              │
│  3. AFTER all edits done   ──►  "$LEDGER" checkpoint post    │
│                                   (detects all changes)      │
│                                                              │
│  4. Git commit             ──►  Attribution auto-recorded    │
│                                   (via post-commit hook)     │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

## Required Commands

### Step 1: Before Writing ANY Code

**You MUST run this command before making any code changes:**

```bash
"$LEDGER" checkpoint pre
```

This command:
- Captures a snapshot of the current file state using Git's tree mechanism
- Detects any human changes since the last AI session (if applicable)
- Creates a session ID to track this coding session

**Expected output:**
```
✓ Pre-checkpoint recorded (session: a1b2c3d4)
```

If human changes are detected:
```
✓ Detected human changes: 2 added, 1 modified, 0 deleted
✓ Pre-checkpoint recorded (session: a1b2c3d4)
```

### Step 2: Write Your Code

After running `checkpoint pre`, proceed with all your code changes:
- Create new files
- Edit existing files
- Delete files
- Refactor code

**No need to track file paths** - CodeLedger automatically detects all changes.

### Step 3: After ALL Edits Are Complete

**You MUST run this command after finishing all code changes:**

```bash
"$LEDGER" checkpoint post
```

This command:
- Compares current state with the pre-checkpoint snapshot
- Records all changed files as AI-authored
- Prepares attribution data for the next commit

**Expected output:**
```
✓ Post-checkpoint recorded (session: a1b2c3d4)
  Added: 3, Modified: 2, Deleted: 0
```

## Complete Examples

### Example 1: Creating a New Feature

**User request:** "Create a utility function in src/utils.ts"

**Correct execution:**
```bash
# Set up LEDGER variable first (one-liner from above)
# ...

# Step 1: Record pre-checkpoint
"$LEDGER" checkpoint pre

# Step 2: Create/edit the file (your actual work)
# ... write code to src/utils.ts ...

# Step 3: Record post-checkpoint
"$LEDGER" checkpoint post
```

### Example 2: Fixing a Bug Across Multiple Files

**User request:** "Fix the authentication bug in the login flow"

**Correct execution:**
```bash
# Step 1: Record pre-checkpoint FIRST
"$LEDGER" checkpoint pre

# Step 2: Fix the bug (may touch multiple files)
# ... edit src/auth/login.ts ...
# ... edit src/auth/session.ts ...
# ... edit tests/auth.test.ts ...

# Step 3: Record post-checkpoint after ALL edits
"$LEDGER" checkpoint post
```

### Example 3: First Time Setup + Code Changes

**User request:** "Set up the project and add a new feature"

**Correct execution:**
```bash
# One-time initialization
"$LEDGER" init
"$LEDGER" install-hooks

# Then follow normal workflow
"$LEDGER" checkpoint pre
# ... write code ...
"$LEDGER" checkpoint post
```

## Important Rules

### DO:
- ✅ Set up the `$LEDGER` variable at the start of coding tasks
- ✅ Run `checkpoint pre` BEFORE your first edit
- ✅ Run `checkpoint post` AFTER your last edit
- ✅ Include all related changes in a single pre/post session
- ✅ Continue with code writing if checkpoint commands fail (but inform the user)

### DON'T:
- ❌ Don't assume `ledger` is in PATH - always use `$LEDGER`
- ❌ Don't run `checkpoint pre` multiple times without a `checkpoint post`
- ❌ Don't forget to run `checkpoint post` after completing edits
- ❌ Don't run checkpoints for non-code tasks (reading, explaining, etc.)

## When to Use Checkpoints

| Task Type | Use Checkpoints? |
|-----------|------------------|
| Writing new code | ✅ Yes |
| Editing existing code | ✅ Yes |
| Creating new files | ✅ Yes |
| Deleting files | ✅ Yes |
| Refactoring code | ✅ Yes |
| Reading/explaining code | ❌ No |
| Answering questions | ❌ No |
| Running tests/builds | ❌ No |

## Error Handling

### If skill bin directory is not found:
```
Error: CodeLedger skill bin directory not found
```
→ The skill may not be properly installed. Check `.claude/skills/codeledger/bin/` exists.

### If binary doesn't have execute permission:
```
Permission denied
```
→ Run: `chmod +x "$LEDGER"`

### If `checkpoint pre` fails:
```
Error: Not a git repository
```
→ This directory is not a Git repository. Checkpoints require Git.

```
Error: CodeLedger not initialized. Run 'ledger init' first
```
→ Run `"$LEDGER" init` to initialize CodeLedger in this repository.

### If `checkpoint post` fails:
```
Error: No active session. Run 'ledger checkpoint pre' first
```
→ You forgot to run `checkpoint pre` before making changes.

```
No changes detected since pre-checkpoint.
```
→ No files were modified between pre and post checkpoints.

### General Guidance:
- If checkpoint commands fail, **continue with your code writing**
- Inform the user about the failure so they can troubleshoot
- The failure should not block your ability to help with code

## Other Useful Commands

After commits are made, users can use these commands to view attribution data:

```bash
# View overall statistics
"$LEDGER" stats

# View attribution for a specific commit
"$LEDGER" show [commit-sha]

# Verify data integrity
"$LEDGER" verify

# Check CodeLedger status
"$LEDGER" status

# Push attribution notes to remote
"$LEDGER" push-notes
```

## Manual Installation (Alternative)

If you prefer to install `ledger` globally in your PATH instead of using the bundled binary:

1. Copy the appropriate binary from `bin/` to a directory in your PATH
2. Rename it to `ledger` (or `ledger.exe` on Windows)
3. Then you can use `ledger` directly instead of `"$LEDGER"`

See the project README.md for detailed installation instructions.

## Summary

**Every time you write or modify code:**

1. Set up `$LEDGER` variable (detect platform, find binary)
2. `"$LEDGER" checkpoint pre` → Start tracking
3. *[Make all your code changes]*
4. `"$LEDGER" checkpoint post` → Finish tracking

This ensures accurate AI code attribution for auditing purposes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jaguarliuu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
