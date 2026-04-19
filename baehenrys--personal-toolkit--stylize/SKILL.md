---
name: stylize
description: Format and organize Obsidian notes with auto-wikilink discovery according to the vault's style guide. Use when the user asks to stylize, format, clean up, or add wikilinks to notes in Notes/, Projects/, or specific files. Supports custom instructions for ordering/styling. Does NOT apply to Daily Notes. Use when this capability is needed.
metadata:
  author: baehenrys
---

# Stylize Skill

Format and organize Obsidian notes according to the vault's style guide with spell/grammar corrections and automatic wikilink discovery.

## Configuration

Read `~/.claude/settings.json` and extract `env.OBSIDIAN_VAULT`.

If `OBSIDIAN_VAULT` is not set, display: "Vault not configured. Run /vault first."

Read vault structure from `<vault_path>/vault-config.yaml`.

## Usage

```
/stylize <path> [--instructions "custom instructions"] [--no-link] [--no-suggest]
```

**Arguments:**
- `<path>` - File or folder to stylize (relative to vault root)
  - `Notes/` - All notes in Notes folder
  - `Projects/` - All notes in Projects folder
  - `Notes/my-note.md` - Specific note
- `--instructions "..."` - Custom instructions for ordering/styling (optional)
- `--no-link` - Skip vault index and wikilink discovery (formatting only)
- `--no-suggest` - Skip the suggestion output for new notes

**Examples:**
```
/stylize Notes/
/stylize Projects/my-project.md
/stylize Notes/ --instructions "order sections alphabetically"
/stylize Notes/my-note.md --no-link
```

## Workflow

When this skill is invoked:

### 1. Check Recent Run Report (Optional)

If stylizing notes that may have been recently created/modified by the thought organizer, check:
```
<vault_path>/Processed/YYYY-MM-DD/run-report.md
```

This report shows:
- All thoughts processed that day with actions (create, append, skip)
- Destinations (wikilinks to notes/projects that were modified)
- Reasoning for each decision

Use this to understand which notes were just generated and may need stylizing.

### 2. Read the Style Guide

Read the vault's style guide at the vault root:
```
<vault_path>/Style Guide.md
```

The style guide is the **single source of truth** for all formatting, naming, linking, and content rules. Apply everything it defines.

### 3. Use Obsidian Syntax Skills

Reference the appropriate Obsidian skills for proper syntax:

**Markdown content** - `obsidian-markdown` skill:
- Wikilinks: `[[note-name]]`, `[[note-name|display text]]`
- Callouts: `> [!note]`, `> [!warning]`
- Frontmatter/properties
- Embeds, tags, block references

**Base embeds** - `obsidian-bases` skill (when notes embed `.base` files):
- Embed syntax: `![[MyBase.base]]` or `![[MyBase.base#View Name]]`
- Base file structure (YAML with filters, formulas, views)

**Canvas embeds** - `json-canvas` skill (when notes embed `.canvas` files):
- Embed syntax: `![[MyCanvas.canvas]]`
- Canvas file structure (JSON with nodes, edges, groups)

### 4. Build Vault Link Index

**Skip this step if `--no-link` is passed.**

Launch **two Explore subagents in parallel** to build a comprehensive index of all linkable entities in the vault. This index is built once per invocation and reused across all notes being stylized.

**Agent 1: "Structure Index"** — Scans Categories/, References/, Projects/

```
Agent tool:
  subagent_type: "Explore"
  description: "Build vault link index - structure"
  prompt: |
    Build a wikilink target index from the Obsidian vault at {vault_path}.

    Read EVERY .md file in these folders:
    - {vault_path}/Categories/
    - {vault_path}/References/
    - {vault_path}/Projects/

    For each file, extract:
    1. Note title (the filename without .md extension)
    2. Frontmatter `categories` values (if any)
    3. Frontmatter `type` values (if any)
    4. Frontmatter `aliases` values (if any)

    Also list ALL filenames (without .md) in {vault_path}/Daily Notes/
    (do NOT read daily note content, just the filenames).

    Return a structured list:
    TITLE: <note title>
    FOLDER: <Categories|References|Projects>
    CATEGORIES: <category links>
    TYPE: <type links>
    ALIASES: <aliases if any>
    ---
    (repeat for each note)

    Then list daily note filenames separately.
```

**Agent 2: "Content Index"** — Scans Notes/, Clippings/

```
Agent tool:
  subagent_type: "Explore"
  description: "Build vault link index - content"
  prompt: |
    Build a wikilink target index from the Obsidian vault at {vault_path}.

    Read EVERY .md file in these folders:
    - {vault_path}/Notes/
    - {vault_path}/Clippings/

    For each file, extract:
    1. Note title (the filename without .md extension — notes do NOT have H1 headings)
    2. Frontmatter `categories` values (if any)
    3. Frontmatter `aliases` values (if any)
    4. Key concepts mentioned in the first paragraph of body content

    Return a structured list:
    TITLE: <note title>
    FOLDER: <Notes|Clippings>
    CATEGORIES: <category links>
    ALIASES: <aliases if any>
    CONCEPTS: <key concepts from first paragraph>
    ---
    (repeat for each note)
```

**Run both agents in parallel.**

After both return, merge results into a single vault entity index. The index contains:
- All resolved targets (existing note titles mapped to their folders)
- All category pages
- All aliases
- Key concepts from note content

### 5. Apply Formatting and Wikilinks

For each note in the target path, apply all rules from the style guide read in Step 2 — including naming, frontmatter, content structure, writing style, and spell/grammar corrections.

#### Wikilink Application

Follow the linking conventions defined in the style guide. Use the vault entity index from Step 4 to identify linkable terms in each note.

Implementation details not covered by the style guide:

- **Context from title**: Extract each note's topic from its filename (notes do not have H1 headings). Do not self-link.
- **Skip zones**: Do not add wikilinks inside frontmatter, existing `[[wikilinks]]`, code blocks, or URLs.
- **Display text**: Use `[[Note Title|mentioned text]]` when the text in the note does not exactly match the target note's filename.

**Processing order per note:**
1. **Navigate**: Run `obsidian open file="<note title>"` to open the note in Obsidian so the user can see changes live
2. Extract the note's topic from its filename
3. Build a list of potential link targets from the vault index (excluding the note itself)
4. Scan the note body (excluding skip zones) for mentions of targets
5. Preview proposed changes and wait for approval (see Output section)
6. Apply wikilinks per the style guide's linking rules, using display text where the mention doesn't match the title exactly

### 5a. Place Detection (Auto)

**Skip this step if `--no-link` is passed.**

While scanning note content in Step 5, detect location mentions that refer to specific, real-world places. This runs automatically as part of the normal stylize flow -- it only creates new reference notes for places that don't exist yet.

First, read the API reference for type mappings and field details:
```
${CLAUDE_PLUGIN_ROOT}/shared/google-maps-api.md
```

#### Get current location (once per invocation)

```bash
CoreLocationCLI -format "%latitude,%longitude"
```
If not installed or fails, proceed without location bias.

#### Detect and look up new locations

Identify location mentions in note content:
- Patterns: "at [Place]", "went to [Place]", "visited [Place]", "dinner at [Place]"
- Restaurant, cafe, bar, hotel, park, trail, or landmark names in context
- Any proper noun referring to a physical location

For each detected place, check the vault index from Step 4. If a reference note already exists, just wikilink it. If no reference note exists:

1. Look up the place via Google Maps:
   ```bash
   echo '{"query": "<place name> <city if mentioned>", "location_bias": [<lat>, <lng>]}' | python "${CLAUDE_PLUGIN_ROOT}/shared/search_place.py"
   ```
   Omit `location_bias` if CoreLocationCLI was unavailable.

2. Present the result for confirmation:
   ```
   Detected: "Taniku Izakaya in Japantown"
   Google Maps: Taniku Izakaya, 1581 Webster St #235, San Francisco, CA 94115
   Type: Restaurant | Rating: 4.5/5 | Price: $$$
   Create reference note? [y/n/skip all places]
   ```

3. **Determine the vault `type`** using the Google `primaryType` and `types` array.
   Read the vault's `Categories/` folder and existing `type` values from place notes
   in `References/` to find the best match. For example, `primaryType: "japanese_restaurant"`
   maps to `[[Restaurants]]`, `primaryType: "national_park"` maps to `[[Parks]]`.

   If no existing vault type fits the Google type:
   - Ask the user: "Google classifies this as '<primaryType>'. No matching vault type exists.
     Create a new type `[[<suggested name>]]`? [y/n/custom]"
   - If confirmed, create the corresponding type category file in `Categories/` following
     the vault's category template pattern
   - If the user provides a custom name, use that instead

4. **Determine the vault `loc`** using the `city` field from the script output.
   Use nested wikilinks for region hierarchy: `["[[City]]", "[[State/Region]]"]`
   (e.g., `["[[Big Sur]]", "[[California]]"]`).

5. If confirmed, create `References/<Place Name>.md` using the appropriate vault template:
   - Read `<vault_path>/Templates/Place Template.md` (or `Restaurant Template.md`
     for restaurants/cafes/bars)
   - Use the template as the base frontmatter structure
   - Fill in the template fields from the API data:
     - `type` -> vault type wikilink from step 3
     - `loc` -> nested wikilinks from step 4
     - `coordinates` -> `[latitude, longitude]` as strings
     - `address` -> formatted as `'["full address"]'`
     - `source` -> `googleMapsUrl`
     - `placeId` -> from API
     - `scoreGoogle` -> from API
     - `price` -> from API (restaurants/cafes/bars only)
     - `created` -> today's date
   - Leave `rating` and `last` empty (user fills these)

6. Wikilink the mention in the source note.

**Rules:** See the vault property mapping table in `google-maps-api.md` for full details.
The key rule: `rating` is the user's personal 1-7 scale -- NEVER auto-fill it from Google.
`type` and `loc` must be wikilink arrays. Type mapping is NOT hardcoded -- always check
the vault's actual Categories/ and existing type values.

**Note:** To backfill metadata on existing notes with empty frontmatter, use `/vault:enrich` instead. Stylize handles place detection for new mentions only.

### 6. Apply Custom Instructions

If `--instructions` provided, apply them after standard formatting. These are for **content organization**, not style overrides:
- Section ordering (e.g., "put Next Steps at the top")
- Content grouping (e.g., "group related items together")
- Structural preferences (e.g., "order sections alphabetically")

### 7. Suggestion Output

**Skip this step if `--no-suggest` is passed.**

After all notes are processed, review the unresolved wikilinks that were created during Step 5. Output a grouped list of suggested new notes:

```
## Suggested New Notes

Based on unresolved wikilinks discovered:

### People
- [[Person Name]] — mentioned in: Note A, Note B

### Companies
- [[Company Name]] — mentioned in: Note C

### Tools
- [[Tool Name]] — mentioned in: Note D

### Concepts
- [[Concept Name]] — mentioned in: Note E
```

This is informational only — do not create notes automatically.

### 8. Preserve Content

**DO:**
- Preserve all original content and meaning
- Keep the author's voice
- Make minimal necessary changes

**DON'T:**
- Add boilerplate ("This note is about...")
- Add meta-commentary
- Expand or pad content
- Over-format with excessive bold/italics
- Delete empty frontmatter properties — they are part of the note's template
- Summarize, paraphrase, or rewrite descriptions in References and Clippings — use the source text verbatim and only strip markup clutter (HTML, nav elements, ads, thumbnail galleries, achievement icons, UI screenshots). Keep relevant images (e.g., header/banner images, primary content images)

## Restrictions

- **DO NOT** stylize files in `Daily Notes/` - these are journal entries
- Only process `.md` files
- Skip files that are already well-formatted (no changes needed)

## Output

### Preview

Before writing any changes, present a preview for each file:

1. Show the file name
2. Summarize what would change (spelling, frontmatter, structure, wikilinks added)
3. Show the specific wikilinks that would be added or changed
4. Show any other notable edits (rewording, formatting fixes)

Wait for user approval before proceeding. The user may:
- **Approve all** — write all changes
- **Approve selectively** — accept some files, reject others
- **Request changes** — modify the proposed edits, then re-preview
- **Cancel** — discard all proposed changes

### Write

After approval, write the accepted changes to the files and confirm completion.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/baehenrys) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
