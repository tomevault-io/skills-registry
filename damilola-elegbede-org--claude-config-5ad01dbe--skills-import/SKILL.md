---
name: skills-import
description: Import skills from external repositories like anthropics/skills. Use when adding new skills. Use when this capability is needed.
metadata:
  author: damilola-elegbede-org
---

# /skills-import

## Usage

```bash
/skills-import                    # Interactive browse and import
/skills-import --list             # List available skills without importing
/skills-import --preview pdf      # View full SKILL.md content before installing
/skills-import --refresh          # Force refresh cached skill listings
```

## Description

Browse and import skills from the official `anthropics/skills` GitHub repository into `system-configs/.claude/skills/`.
Skills provide focused domain expertise for specific file formats and workflows.

## Expected Output

### List Mode

```text
/skills-import --list

Available Skills from anthropics/skills (16 skills):

  #   Name                    Description
  -   ----------------------  -----------------------------------------
  1   algorithmic-art         Create algorithmic and generative art
  2   brand-guidelines        Apply brand guidelines to content
  3   docx                    Create and edit Word documents
  ...

Run /skills-import to select and install skills
```

### Preview Mode

```text
/skills-import --preview pdf

Skill Preview: pdf
----------------------------------------------------------------------

Source: anthropics/skills
Files: SKILL.md, REFERENCE.md, LICENSE.txt (3 files)

[Full SKILL.md content displayed]

----------------------------------------------------------------------
To install: /skills-import, then select "pdf"
```

### Interactive Import

```text
/skills-import

Fetching skills from anthropics/skills...

Available Skills:
  1. algorithmic-art     Create algorithmic and generative art
  2. docx                Create and edit Word documents [installed]
  3. pdf                 Process and create PDF files
  ...

Enter skill numbers (comma-separated, or 'all'): 2,3

Skills to import:
  - docx: Create and edit Word documents
  - pdf: Process and create PDF files

Downloading docx...
Installed docx (3 files)

Downloading pdf...
Installed pdf (4 files)

Validating imported skills...
All 2 imported skills validated

Next steps:
  1. Review: system-configs/.claude/skills/
  2. Run /sync to deploy to ~/.claude/
```

## Behavior

### Execution Flow

```yaml
Phase 1 - Fetch Skills:
  1. Check cache: .tmp/skills-cache/anthropics-skills.json
     - If exists and < 1 hour old: use cache
     - Else: fetch from GitHub API
  2. Fetch skill listing:
     gh api repos/anthropics/skills/contents/skills
  3. For each skill directory, fetch SKILL.md for description
  4. Store in cache with timestamp

Phase 2 - Display & Select (interactive mode):
  1. Display numbered list with [installed] tags
  2. Prompt for selection (comma-separated or 'all')
  3. Confirm overwrite for already-installed skills

Phase 3 - Download & Install:
  1. For each selected skill:
     - Create: system-configs/.claude/skills/{name}/
     - Download all files recursively via GitHub API
     - Handle subdirectories (reference/, scripts/, etc.)
  2. Run validation: python scripts/validate-skills.py
  3. Output next steps
```

### Flag Handling

| Flag | Action |
|------|--------|
| `--list` | Display available skills, exit without importing |
| `--preview <name>` | Show full SKILL.md content for specified skill |
| `--refresh` | Delete cache, force fresh fetch from GitHub |
| (no flags) | Interactive browse and import workflow |

### GitHub API Commands

```bash
# List all skills
gh api repos/anthropics/skills/contents/skills

# Get skill directory contents
gh api repos/anthropics/skills/contents/skills/pdf

# Download file content (base64 encoded)
gh api repos/anthropics/skills/contents/skills/pdf/SKILL.md | jq -r '.content' | base64 -d
```

### Cache Strategy

```yaml
Location: .tmp/skills-cache/anthropics-skills.json
TTL: 1 hour
Structure:
  timestamp: ISO 8601
  skills:
    - name: pdf
      description: Process and create PDF files
      files: [SKILL.md, REFERENCE.md]
```

### Error Handling

| Scenario | Action |
|----------|--------|
| Rate limit (403) | Display message, suggest `gh auth status` |
| Network failure | Abort with clear error |
| Skill not found | Skip with warning, continue others |
| Invalid skill format | Skip with warning, continue others |

### Output Locations

```yaml
Installed Skills: system-configs/.claude/skills/{name}/
Cache: .tmp/skills-cache/anthropics-skills.json
```

## Implementation

Execute these phases in order:

### Parse Arguments

```bash
# Determine mode from $ARGUMENTS
MODE="interactive"  # default
SKILL_NAME=""

if [[ "$ARGUMENTS" == *"--list"* ]]; then
  MODE="list"
elif [[ "$ARGUMENTS" == *"--preview"* ]]; then
  MODE="preview"
  # Extract skill name after --preview
  SKILL_NAME=$(echo "$ARGUMENTS" | sed 's/.*--preview[[:space:]]\+//' | awk '{print $1}')
elif [[ "$ARGUMENTS" == *"--refresh"* ]]; then
  rm -rf .tmp/skills-cache/
  echo "Cache cleared, fetching fresh data..."
fi
```

### Phase 1: Fetch Available Skills

```bash
# Create cache directory
mkdir -p .tmp/skills-cache

# Check cache freshness (1 hour = 3600 seconds)
CACHE_FILE=".tmp/skills-cache/anthropics-skills.json"
CACHE_AGE=3600

if [[ -f "$CACHE_FILE" ]]; then
  FILE_AGE=$(($(date +%s) - $(stat -f %m "$CACHE_FILE" 2>/dev/null || stat -c %Y "$CACHE_FILE")))
  if [[ $FILE_AGE -lt $CACHE_AGE ]]; then
    echo "Using cached skill list ($(($CACHE_AGE - $FILE_AGE))s until refresh)"
  else
    FETCH_FRESH=true
  fi
else
  FETCH_FRESH=true
fi

if [[ "$FETCH_FRESH" == "true" ]]; then
  echo "Fetching skills from anthropics/skills..."

  # Fetch skill directories
  SKILLS_RAW=$(gh api repos/anthropics/skills/contents/skills 2>&1)

  if [[ $? -ne 0 ]]; then
    echo "Failed to fetch skills: $SKILLS_RAW"
    echo "Check: gh auth status"
    exit 1
  fi

  # Parse skill names
  SKILL_NAMES=$(echo "$SKILLS_RAW" | jq -r '.[] | select(.type == "dir") | .name')

  # Build cache with descriptions
  echo '{"timestamp":"'$(date -u +%Y-%m-%dT%H:%M:%SZ)'","skills":[' > "$CACHE_FILE"

  FIRST=true
  for SKILL in $SKILL_NAMES; do
    # Fetch SKILL.md for description
    SKILL_MD=$(gh api "repos/anthropics/skills/contents/skills/$SKILL/SKILL.md" 2>/dev/null)
    if [[ $? -eq 0 ]]; then
      CONTENT=$(echo "$SKILL_MD" | jq -r '.content' | base64 -d 2>/dev/null)
      DESC=$(echo "$CONTENT" | grep -A1 "^description:" | tail -1 | sed 's/description:[[:space:]]*//' | head -c 60)
      if [[ -z "$DESC" ]]; then
        DESC="No description available"
      fi

      FILES=$(gh api "repos/anthropics/skills/contents/skills/$SKILL" 2>/dev/null | jq -r '.[].name' | tr '\n' ',' | sed 's/,$//')

      if [[ "$FIRST" != "true" ]]; then
        echo "," >> "$CACHE_FILE"
      fi
      FIRST=false

      echo "{\"name\":\"$SKILL\",\"description\":\"$DESC\",\"files\":\"$FILES\"}" >> "$CACHE_FILE"
    fi
  done

  echo ']}' >> "$CACHE_FILE"
fi
```

### Phase 2: Display Skills

```bash
# Read from cache
SKILLS_JSON=$(cat "$CACHE_FILE")
SKILL_COUNT=$(echo "$SKILLS_JSON" | jq '.skills | length')

# Check which are installed
INSTALLED_DIR="system-configs/.claude/skills"

echo ""
echo "Available Skills from anthropics/skills ($SKILL_COUNT skills):"
echo ""
printf "  %-4s %-24s %s\n" "#" "Name" "Description"
printf "  %-4s %-24s %s\n" "-" "------------------------" "-----------------------------------------"

IDX=1
echo "$SKILLS_JSON" | jq -r '.skills[] | "\(.name)|\(.description)"' | while IFS='|' read -r NAME DESC; do
  INSTALLED=""
  if [[ -d "$INSTALLED_DIR/$NAME" ]]; then
    INSTALLED=" [installed]"
  fi
  printf "  %-4s %-24s %s%s\n" "$IDX" "$NAME" "$DESC" "$INSTALLED"
  IDX=$((IDX + 1))
done
```

### Phase 3: Interactive Selection and Download

```bash
if [[ "$MODE" == "list" ]]; then
  echo ""
  echo "Run /skills-import to select and install skills"
  exit 0
fi

if [[ "$MODE" == "preview" ]]; then
  if [[ -z "$SKILL_NAME" ]]; then
    echo "Error: --preview requires a skill name"
    echo "Usage: /skills-import --preview <skill-name>"
    exit 1
  fi

  # Check if skill exists in cache
  SKILL_EXISTS=$(echo "$SKILLS_JSON" | jq -r --arg name "$SKILL_NAME" '.skills[] | select(.name == $name) | .name')
  if [[ -z "$SKILL_EXISTS" ]]; then
    echo "Error: Skill '$SKILL_NAME' not found. Run /skills-import --list to see available skills."
    exit 1
  fi

  echo ""
  echo "Preview: $SKILL_NAME"
  echo "---"

  # Fetch and display SKILL.md content
  SKILL_MD=$(gh api "repos/anthropics/skills/contents/skills/$SKILL_NAME/SKILL.md" 2>/dev/null)
  if [[ $? -eq 0 ]]; then
    echo "$SKILL_MD" | jq -r '.content' | base64 -d
  else
    echo "Error: Failed to fetch skill content"
    exit 1
  fi

  echo ""
  echo "---"
  echo "Install with: /skills-import $SKILL_NAME"
  exit 0
fi

# Interactive mode - use AskUserQuestion for selection
# Download each selected skill to system-configs/.claude/skills/{name}/

INSTALLED_DIR="system-configs/.claude/skills"
mkdir -p "$INSTALLED_DIR"

for SKILL in $SKILL_NAMES; do
  echo ""
  echo "Downloading $SKILL..."

  mkdir -p "$INSTALLED_DIR/$SKILL"

  FILES=$(gh api "repos/anthropics/skills/contents/skills/$SKILL" 2>/dev/null)

  echo "$FILES" | jq -r '.[] | "\(.name)|\(.type)|\(.path)"' | while IFS='|' read -r NAME TYPE PATH; do
    if [[ "$TYPE" == "file" ]]; then
      CONTENT=$(gh api "repos/anthropics/skills/contents/$PATH" 2>/dev/null | jq -r '.content' | base64 -d)
      echo "$CONTENT" > "$INSTALLED_DIR/$SKILL/$NAME"
    elif [[ "$TYPE" == "dir" ]]; then
      mkdir -p "$INSTALLED_DIR/$SKILL/$NAME"
      SUBFILES=$(gh api "repos/anthropics/skills/contents/$PATH" 2>/dev/null)
      echo "$SUBFILES" | jq -r '.[] | select(.type == "file") | "\(.name)|\(.path)"' | while IFS='|' read -r SUBNAME SUBPATH; do
        SUBCONTENT=$(gh api "repos/anthropics/skills/contents/$SUBPATH" 2>/dev/null | jq -r '.content' | base64 -d)
        echo "$SUBCONTENT" > "$INSTALLED_DIR/$SKILL/$NAME/$SUBNAME"
      done
    fi
  done

  echo "Installed $SKILL"
done

# Validation
echo ""
echo "Validating imported skills..."
if [[ -f "scripts/validate-skills.py" ]]; then
  python scripts/validate-skills.py
fi

echo ""
echo "Next steps:"
echo "  1. Review: system-configs/.claude/skills/"
echo "  2. Run /sync to deploy to ~/.claude/"
```

## Prerequisites

- `gh` CLI authenticated with GitHub (`gh auth status`)
- `jq` for JSON parsing
- Network access to GitHub API

## Notes

- **Cache location**: `.tmp/skills-cache/anthropics-skills.json`
- **1-hour cache TTL**: Use `--refresh` to force update
- **Preserves original format**: Does not transform skills to local template
- **Subdirectory support**: Downloads entire skill directory including references/, scripts/
- **Integration**: Run `/sync` after import to deploy skills to `~/.claude/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/damilola-elegbede-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
