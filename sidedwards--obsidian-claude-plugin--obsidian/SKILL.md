---
name: obsidian
description: Interact with Obsidian vaults using the Obsidian CLI. Use when the user wants to manage notes, search vaults, create/read/edit files, manage tags, tasks, properties, plugins, sync, publish, or any other Obsidian vault operation from the terminal. Use when this capability is needed.
metadata:
  author: sidedwards
---

# Obsidian CLI

Control Obsidian from the terminal. Requires Obsidian 1.12+ running with CLI enabled.

## Quick Usage

Single command: `obsidian <command> [params] [flags]`
Interactive TUI: `obsidian` (then type commands interactively)

## Syntax Rules

- **Parameters** take values: `parameter=value` or `parameter="value with spaces"`
- **Flags** are boolean switches: `silent`, `overwrite`, `total`
- **Newlines** in content: use `\n` for newlines, `\t` for tabs
- **Vault targeting**: `vault=<name>` (defaults to current working directory vault)
- **File targeting**: `file=<name>` (wikilink resolution) or `path=<path>` (exact path)
- **Copy output**: append `--copy` to any command

## Core Commands

### Files & Notes

```bash
# List, read, create, modify files
obsidian files                              # list all vault files
obsidian files folder=projects ext=md       # filter by folder/extension
obsidian read file="My Note"                # read file contents (wikilink)
obsidian read path="folder/note.md"         # read file contents (exact path)
obsidian create name="New Note" content="# Title\nBody text"
obsidian create name="From Template" template="Meeting Notes"
obsidian create path="projects/idea.md" content="content" overwrite
obsidian open file="My Note"                # open in Obsidian
obsidian open file="My Note" newtab         # open in new tab
obsidian append file="My Note" content="\n- New item"
obsidian prepend file="My Note" content="Top content"
obsidian move file="Old Name" to="new/path.md"
obsidian delete file="My Note"              # trash
obsidian delete file="My Note" permanent    # permanent delete
obsidian file file="My Note"                # show file info
```

### Search

```bash
obsidian search query="search term"
obsidian search query="regex pattern" format=json matches
obsidian search query="keyword" path=projects limit=10 total
obsidian search:open query="open in UI"
```

### Daily Notes

```bash
obsidian daily                    # open today's daily note
obsidian daily silent             # create without opening
obsidian daily:read               # read today's daily note
obsidian daily:append content="- Task done"
obsidian daily:prepend content="## Morning\nNotes here"
```

### Tags

```bash
obsidian tags                          # list all tags
obsidian tags file="My Note"           # tags in specific file
obsidian tags sort=count counts        # sorted by frequency
obsidian tag name=project total verbose
```

### Tasks

```bash
obsidian tasks                         # all tasks
obsidian tasks todo                    # uncompleted only
obsidian tasks done                    # completed only
obsidian tasks file="My Note"          # tasks in specific file
obsidian tasks daily                   # tasks in daily note
obsidian task ref="path/note.md:15" toggle   # toggle task on line 15
obsidian task file="My Note" line=15 done    # mark done
```

### Properties (Frontmatter)

```bash
obsidian properties                            # list all properties
obsidian properties file="My Note" format=yaml
obsidian property:read name=status file="My Note"
obsidian property:set name=status value=done file="My Note"
obsidian property:set name=tags value="[a,b]" type=list file="My Note"
obsidian property:remove name=draft file="My Note"
obsidian aliases all                           # list all aliases
```

### Links & Graph

```bash
obsidian backlinks file="My Note"        # what links to this
obsidian backlinks file="My Note" counts total
obsidian links file="My Note"            # outgoing links
obsidian unresolved total counts         # broken links
obsidian orphans                         # no backlinks
obsidian deadends                        # no outgoing links
```

### Outline & Wordcount

```bash
obsidian outline file="My Note"              # headings
obsidian outline file="My Note" format=tree
obsidian wordcount file="My Note"
obsidian wordcount file="My Note" words      # words only
obsidian wordcount file="My Note" characters # chars only
```

### Bookmarks

```bash
obsidian bookmarks                    # list bookmarks
obsidian bookmarks total verbose
obsidian bookmark file="My Note"      # bookmark a file
obsidian bookmark file="My Note" subpath="Section"
obsidian bookmark search="query" title="Saved Search"
obsidian bookmark url="https://..." title="Web Link"
```

### Templates

```bash
obsidian templates                          # list templates
obsidian template:read name="Meeting Notes"
obsidian template:read name="Meeting Notes" title="Q1 Review" resolve
obsidian template:insert name="Meeting Notes"
```

### Plugins

```bash
obsidian plugins                               # all installed
obsidian plugins:enabled filter=community versions
obsidian plugin id=dataview                    # plugin info
obsidian plugin:enable id=dataview
obsidian plugin:disable id=dataview
obsidian plugin:install id=dataview enable     # install + enable
obsidian plugin:uninstall id=dataview
obsidian plugin:reload id=my-plugin            # dev reload
obsidian plugins:restrict on                   # restricted mode
```

### Sync

```bash
obsidian sync:status                   # sync status + usage
obsidian sync on                       # resume sync
obsidian sync off                      # pause sync
obsidian sync:history file="My Note"   # version history
obsidian sync:read file="My Note" version=2
obsidian sync:restore file="My Note" version=2
obsidian sync:deleted                  # list deleted files
```

### Publish

```bash
obsidian publish:site                  # site info
obsidian publish:list total            # published files
obsidian publish:status                # pending changes
obsidian publish:add file="My Note"    # publish file
obsidian publish:add changed           # publish all changes
obsidian publish:remove file="My Note" # unpublish
obsidian publish:open file="My Note"   # open on site
```

### Vault & Workspace

```bash
obsidian vault                         # vault info
obsidian vault info=size               # specific info
obsidian vaults                        # list all vaults
obsidian folders                       # list folders
obsidian folder path=projects info=size

obsidian workspaces                    # list workspaces
obsidian workspace:save name=coding
obsidian workspace:load name=coding
obsidian workspace:delete name=old
obsidian workspace                     # show workspace tree
obsidian tabs                          # list open tabs
obsidian recents                       # recent files
```

### Bases (Database)

```bash
obsidian bases                                  # list .base files
obsidian base:views file="My Base"
obsidian base:create name="New Item" content="data"
obsidian base:query file="My Base" format=json
obsidian base:query file="My Base" view="Table View" format=csv
```

### File History (Local Recovery)

```bash
obsidian history file="My Note"          # list versions
obsidian history:list                     # all files with history
obsidian history:read file="My Note" version=1
obsidian history:restore file="My Note" version=1
obsidian diff file="My Note"             # compare versions
obsidian diff file="My Note" from=1 to=3
```

### Themes & Snippets

```bash
obsidian themes versions
obsidian theme name="Minimal"
obsidian theme:set name="Minimal"
obsidian theme:install name="Minimal" enable
obsidian theme:uninstall name="Minimal"
obsidian snippets
obsidian snippet:enable name="custom"
obsidian snippet:disable name="custom"
```

### Developer

```bash
obsidian eval code="app.vault.getFiles().length"
obsidian dev:console limit=20 level=error
obsidian dev:errors
obsidian dev:screenshot path="screenshot.png"
obsidian dev:dom selector=".workspace" text
obsidian dev:css selector=".nav-header" prop=background
obsidian devtools
```

### Other

```bash
obsidian commands                        # list command palette commands
obsidian command id="editor:toggle-bold" # execute command
obsidian hotkeys                         # list hotkeys
obsidian random                          # open random note
obsidian random:read folder=journal      # read random note
obsidian unique name="Zettel"            # create unique note
obsidian web url="https://example.com"   # open in web viewer
obsidian version
obsidian reload
obsidian restart
```

## Guidelines

When using the Obsidian CLI on behalf of the user:

1. **Check vault context first**: Run `obsidian vault` to confirm which vault is active if uncertain
2. **Use `file=` for note names** (wikilink-style resolution) and `path=` for exact file paths
3. **Prefer non-destructive operations**: Use `read` before `append`/`prepend` to understand existing content
4. **Escape content properly**: Use `\n` for newlines in content parameters, quote values with spaces
5. **Use `silent` flag** when creating/modifying notes that don't need to be opened in the UI
6. **Use `format=json`** for search results when you need to parse the output programmatically
7. **Batch operations**: For multiple operations, consider the TUI mode or chain commands

For the full command reference with all parameters and flags, see [reference.md](reference.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sidedwards) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
