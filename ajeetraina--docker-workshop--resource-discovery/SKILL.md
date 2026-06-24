---
name: resource-discovery
description: Search strategies for finding new Docker cagent resources Use when this capability is needed.
metadata:
  author: ajeetraina
---

# Resource Discovery Skill

## Search Queries

### Blogs
- "docker cagent" blog
- site:dev.to cagent docker
- site:docker.com/blog cagent
- "cagent" "multi-agent" tutorial

### GitHub Repos
- Search: "cagent" in name/description
- Topic: docker-cagent
- Filename: cagent.yaml or cagent-*.yaml
- Filter: pushed last 30 days, has stars

### MCP Servers
- "mcp-server docker" on GitHub
- "type: mcp" "ref: docker" in YAML files

## Quality Scoring (1-5)
- 5: Official Docker content, major publication
- 4: Known author, active community project
- 3: Decent blog post, working example
- 2: Basic content, minimal repo
- 1: Low quality or not cagent-specific

Only suggest resources scoring 3+.

## Deduplication
Always read current README.md first and compare URLs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ajeetraina) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
