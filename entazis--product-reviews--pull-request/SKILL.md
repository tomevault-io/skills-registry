---
name: pull-request
description: Prepare a high-quality pull request for this repository: keep diffs small, ensure gofmt/goimports, run appropriate tests, and write a clear summary + test plan. Use when the user asks to open a PR, prepare changes for review, or improve PR descriptions. Use when this capability is needed.
metadata:
  author: entazis
---

# Pull Request

## Pre-flight checklist

- [ ] Scope is focused; no unrelated refactors.
- [ ] Code is formatted (`gofmt`, `goimports`).
- [ ] No secrets checked in (env files, credentials, tokens).
- [ ] Behavior changes are tested (at least targeted tests; full suite when appropriate).
- [ ] For review mutations: aggregates update in the same DB transaction and outbox events are emitted (if relevant).
- [ ] Product responses still do **not** include reviews (if touching product endpoints).

## Testing guidance

Default (unit tests, containerized toolchain):

```bash
docker run --rm -v "$PWD":/src -w /src golang:1.25.6-alpine sh -c "go test ./..."
```

Optional integration concurrency test (when relevant):

```bash
RUN_INTEGRATION=1 docker run --rm -v "$PWD":/src -w /src golang:1.25.6-alpine sh -c "go test ./... -run TestConcurrentReviewCreates_UpdateAggregates"
```

## PR description template

Use this structure:

```markdown
## Summary
- <What changed and why>
- <Any user-facing/API impact>

## Test plan
- [ ] `go test ./...` (or targeted packages)
- [ ] Manual check: <endpoint / scenario> (if applicable)
- [ ] Integration test (if applicable)

## Notes
- <Risks, migrations, rollout considerations>
```

## Review-ready final pass

1. Re-read the diff like a reviewer.
2. Ensure errors are handled and logs are not leaking sensitive info.
3. Ensure docs/config are updated if behavior or env vars changed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/entazis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
