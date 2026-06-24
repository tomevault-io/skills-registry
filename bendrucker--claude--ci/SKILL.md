---
name: gitlabci
description: Working with GitLab CI/CD pipelines and jobs. Use when viewing pipelines, debugging jobs, or validating CI configuration. Use when this capability is needed.
metadata:
  author: bendrucker
---
# GitLab CI/CD

Working with GitLab CI/CD pipelines and jobs via `glab ci`.

## Key Commands

```bash
glab ci status             # Pipeline status for current branch
glab ci list               # List recent pipelines
glab ci lint               # Validate .gitlab-ci.yml
glab ci trace <job-id>     # Watch job logs in real-time
glab ci retry <job-id>     # Retry a failed job
glab ci run                # Trigger pipeline for current branch
```

Use `glab ci --help` and `glab ci <command> --help` for full options.

## Debugging Failures

- `glab ci view` - Interactive view of pipeline jobs (can also trace/retry from here)
- `glab ci trace <job-id>` - Stream job logs in real-time
- `glab ci retry <job-id>` - Retry a failed job

## Configuration

For `.gitlab-ci.yml` syntax, see `/ci/yaml/` in [GitLab docs](https://docs.gitlab.com/ci/yaml/).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bendrucker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
