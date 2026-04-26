---
name: worker-gitlab
description: GitLab access patterns, PAT, and repository locations Use when this capability is needed.
metadata:
  author: eron1703
---

# Worker GitLab Knowledge

## GitLab Access

### Group: flow-master
- **Privacy**: Private group
- **Source of Truth**: FlowMaster services are deployed from GitLab repos

### Clone Pattern
```bash
git clone https://oauth2:<PAT>@gitlab.com/flow-master/<repo>.git
```

### Push Pattern
```bash
git push https://oauth2:<PAT>@gitlab.com/flow-master/<repo>.git <branch>
```

## Critical Repositories

### Frontend
- **IMPORTANT**: Use **flowmaster-frontend-nextjs** (NOT frontend-nextjs on GitLab)
- GitHub Mirror exists at HCB-Consulting-ME org

### Backend Services
All 29 services are in the flow-master group on GitLab.

## GitHub Organization

### HCB-Consulting-ME
- **Privacy**: Public
- **Push Method**: Standard SSH or HTTPS (no PAT needed, SSH keys from ~/.ssh/)

## Repository Sync Pattern
- GitLab is source of truth for CI/CD and deployments
- GitHub is public mirror/reference
- Deployment pipelines trigger from GitLab pushes

## Key Facts
- Always use GitLab as primary source for service code
- PAT expires June 2026 (regenerate before expiration)
- flowmaster-frontend-nextjs is the correct frontend repo name on GitLab
- SSH config can be used for GitHub (no special credentials needed)

> For GitLab credentials and repo URLs, use commander-mcp: get_credential("gitlab_pat"), get_context_repos

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eron1703) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
