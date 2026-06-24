---
name: bootstrap-qcow2-check-pr-feedback
description: Fetch and summarize GitHub PR feedback for bootstrap-qcow2 (issue comments, review comments, and reviews) using Bootstrap::GitHubUtils, to manually check for new review notes after someone completes a PR review. Use when this capability is needed.
metadata:
  author: embedconsult
---

# Check PR feedback (manual trigger)

Use this after a reviewer finishes leaving comments to pull the latest feedback into the container.

## Fetch feedback

From the repo root:

```sh
./bin/bq2 github-pr-feedback --pr <PR_NUMBER> --pretty
```

## Notes

- Requires GitHub API access; defaults to `/work/.git-credentials` when present (override with `--credentials`).
- Pass `--repo owner/name` when running outside a git checkout (e.g., staged snapshots) and inference fails.
- Endpoints queried:
  - `/pulls/:number/comments` (review comments on diffs)
  - `/issues/:number/comments` (PR conversation thread)
  - `/pulls/:number/reviews` (submitted reviews)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/embedconsult) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
