---
name: grepai-workspaces
description: Configure multi-project workspaces in GrepAI. Use this skill for monorepos and multiple related projects. Use when this capability is needed.
metadata:
  author: yoanbernabeu
---

# GrepAI Workspaces

This skill covers using GrepAI workspaces to manage multiple related projects with a unified search index.

## When to Use This Skill

- Working with monorepos
- Searching across multiple related projects
- Managing microservices architecture
- Organizing large codebases

## What are Workspaces?

Workspaces allow you to:
- Group multiple projects together
- Search across all projects at once
- Or search specific projects within the workspace
- Share configuration across projects

## Creating a Workspace

```bash
grepai workspace create my-workspace
```

Output:
```
✅ Workspace 'my-workspace' created
   Location: ~/.grepai/workspaces/my-workspace/

   Next: Add projects with 'grepai workspace add'
```

## Adding Projects

```bash
# Add first project
grepai workspace add my-workspace /path/to/frontend

# Add more projects
grepai workspace add my-workspace /path/to/backend
grepai workspace add my-workspace /path/to/shared-lib
```

Output:
```
✅ Added '/path/to/frontend' to workspace 'my-workspace'

   Projects in workspace:
   1. frontend (/path/to/frontend)

   Run 'grepai watch --workspace my-workspace' to index all projects.
```

## Listing Workspaces

```bash
grepai workspace list
```

Output:
```
📁 Workspaces

1. my-workspace
   - Projects: 3
   - Status: Not indexed

2. work-projects
   - Projects: 5
   - Status: Indexed (updated 2h ago)
```

## Viewing Workspace Details

```bash
grepai workspace show my-workspace
```

Output:
```
📁 Workspace: my-workspace

Projects:
1. frontend
   Path: /path/to/frontend
   Files: 450
   Last indexed: 2025-01-28 10:30

2. backend
   Path: /path/to/backend
   Files: 320
   Last indexed: 2025-01-28 10:30

3. shared-lib
   Path: /path/to/shared-lib
   Files: 85
   Last indexed: 2025-01-28 10:30

Total: 855 files, 4,200 chunks
```

## Indexing a Workspace

```bash
# Index all projects in workspace
grepai watch --workspace my-workspace
```

Output:
```
🔍 Indexing workspace 'my-workspace'

   [1/3] frontend...
         Found 450 files, 2,100 chunks
   [2/3] backend...
         Found 320 files, 1,500 chunks
   [3/3] shared-lib...
         Found 85 files, 600 chunks

   Total: 855 files, 4,200 chunks indexed
   Watching for changes...
```

### Background Indexing

```bash
grepai watch --workspace my-workspace --background
```

## Searching Workspaces

### Search All Projects

```bash
grepai search --workspace my-workspace "user authentication"
```

Results include project context:
```
Score: 0.89 | [backend] src/auth/middleware.go:15-45
──────────────────────────────────────────
func AuthMiddleware() ...

Score: 0.85 | [frontend] src/hooks/useAuth.ts:10-30
──────────────────────────────────────────
export function useAuth() ...

Score: 0.78 | [shared-lib] src/types/auth.ts:5-25
──────────────────────────────────────────
export interface AuthState ...
```

### Search Specific Project

```bash
grepai search --workspace my-workspace --project frontend "form validation"
```

Only searches the frontend project.

## Workspace Status

```bash
grepai workspace status my-workspace
```

Output:
```
📊 Workspace Status: my-workspace

Projects: 3
Total files: 855
Total chunks: 4,200
Index size: 45 MB

Per-project breakdown:
┌─────────────┬───────┬────────┬──────────┐
│ Project     │ Files │ Chunks │ Updated  │
├─────────────┼───────┼────────┼──────────┤
│ frontend    │ 450   │ 2,100  │ 2h ago   │
│ backend     │ 320   │ 1,500  │ 2h ago   │
│ shared-lib  │ 85    │ 600    │ 2h ago   │
└─────────────┴───────┴────────┴──────────┘

Daemon: Running (PID 12345)
```

## Removing Projects from Workspace

```bash
grepai workspace remove my-workspace /path/to/old-project
```

## Deleting a Workspace

```bash
grepai workspace delete my-workspace
```

This removes the workspace configuration but NOT the project files.

## Workspace Configuration

Workspaces store config in `~/.grepai/workspaces/<name>/`:

```
~/.grepai/workspaces/my-workspace/
├── workspace.yaml    # Workspace config
├── index.gob         # Combined index
└── symbols.gob       # Combined symbols
```

### workspace.yaml

```yaml
name: my-workspace
created: 2025-01-28T10:00:00Z

projects:
  - name: frontend
    path: /path/to/frontend
  - name: backend
    path: /path/to/backend
  - name: shared-lib
    path: /path/to/shared-lib

# Optional: override global config per workspace
embedder:
  provider: ollama
  model: nomic-embed-text

ignore:
  - node_modules
  - dist
```

## MCP with Workspaces

Use workspaces with MCP servers:

```json
{
  "mcpServers": {
    "grepai": {
      "command": "grepai",
      "args": ["mcp-serve", "--workspace", "my-workspace"]
    }
  }
}
```

## Use Cases

### Monorepo

```bash
# Full monorepo
grepai workspace create monorepo
grepai workspace add monorepo /path/to/monorepo/apps/web
grepai workspace add monorepo /path/to/monorepo/apps/mobile
grepai workspace add monorepo /path/to/monorepo/packages/ui
grepai workspace add monorepo /path/to/monorepo/packages/core
```

### Microservices

```bash
# Related microservices
grepai workspace create services
grepai workspace add services /path/to/user-service
grepai workspace add services /path/to/order-service
grepai workspace add services /path/to/payment-service
grepai workspace add services /path/to/notification-service
```

### Frontend + Backend

```bash
# Full stack
grepai workspace create fullstack
grepai workspace add fullstack /path/to/frontend
grepai workspace add fullstack /path/to/backend
grepai workspace add fullstack /path/to/shared-types
```

## Cross-Project Search Examples

### Find All API Endpoints

```bash
grepai search --workspace services "REST API endpoint handler"
```

### Find Shared Type Usage

```bash
grepai search --workspace fullstack "User interface definition"
```

### Find Cross-Project Dependencies

```bash
# What calls the shared auth library?
grepai trace callers --workspace fullstack "validateToken"
```

## Best Practices

1. **Name descriptively:** `ecommerce-stack` not `ws1`
2. **Group related projects:** Only projects that you'd search together
3. **One daemon per workspace:** Run `grepai watch --workspace`
4. **Use project filter:** When you know which project to search
5. **Update after major changes:** Re-index after adding/removing files

## Common Issues

❌ **Problem:** Workspace not found
✅ **Solution:** Check workspace exists: `grepai workspace list`

❌ **Problem:** Project paths changed
✅ **Solution:** Remove old path, add new path:
```bash
grepai workspace remove my-workspace /old/path
grepai workspace add my-workspace /new/path
```

❌ **Problem:** Search returns mixed results
✅ **Solution:** Use `--project` flag to filter:
```bash
grepai search --workspace ws --project backend "query"
```

## Output Format

Workspace overview:

```
📁 Workspace: my-workspace

Configuration:
- Location: ~/.grepai/workspaces/my-workspace/
- Created: 2025-01-28

Projects (3):
┌─────────────┬──────────────────────────┬───────┐
│ Name        │ Path                      │ Files │
├─────────────┼──────────────────────────┼───────┤
│ frontend    │ /path/to/frontend        │ 450   │
│ backend     │ /path/to/backend         │ 320   │
│ shared-lib  │ /path/to/shared-lib      │ 85    │
└─────────────┴──────────────────────────┴───────┘

Commands:
- Index: grepai watch --workspace my-workspace
- Search: grepai search --workspace my-workspace "query"
- Status: grepai workspace status my-workspace
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yoanbernabeu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
