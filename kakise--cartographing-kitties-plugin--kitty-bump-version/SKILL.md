---
name: kitty-bump-version
description: | Use when this capability is needed.
metadata:
  author: Kakise
---

Cut a release for the current repository.

## Preconditions

Before doing anything, verify these and bail with an explanation if any fail:

1. The working tree is clean (`git status --porcelain` empty).
2. The current branch is the release branch (look at `pyproject.toml` /
   `CONTRIBUTING.md` / `CLAUDE.md`; default to `main` if unspecified). Reject
   other branches unless the user passes an explicit override.
3. Local branch is up to date with `origin/<branch>` (`git fetch` then compare
   `git rev-parse HEAD` to `git rev-parse @{u}`).
4. The project uses VCS-derived versioning. Confirm one of:
   - `pyproject.toml` declares `dynamic = ["version"]` AND has either
     `[tool.uv-dynamic-versioning]`, `[tool.hatch.version] source = "vcs"`,
     or a `setuptools-scm` config.
   - `package.json` has a release-please / changesets setup.
   If none of these exist, surface that to the user and stop — this skill
   does not edit a hardcoded `version = "x.y.z"` field.

## Pick the next version

1. Read the most recent semver tag: `git tag -l "v[0-9]*" --sort=-v:refname | head -1`.
2. If `$ARGUMENTS` is `major|minor|patch`, bump from that tag using SemVer.
   If `$ARGUMENTS` is `vX.Y.Z`, use that exact version.
   Otherwise, propose a bump level based on the commits since the tag (look
   at the subjects of `git log <last-tag>..HEAD --format=%s`):
   - Any `feat!:`, `BREAKING CHANGE:`, MCP-tool removal, schema change → MAJOR
   - Any `feat:`, new language, new skill, new migration → MINOR
   - Only `fix:`, `chore:`, `docs:`, dependency bumps → PATCH
   Then ask the user to confirm via AskUserQuestion (skip the prompt when
   running in a pipeline mode like `kitty:lfg` and pick the recommended
   option silently).

## Tag and push

```bash
git tag -a vX.Y.Z -m "vX.Y.Z — <one-line summary>"
git push origin vX.Y.Z
```

Skip the `git push` when `--no-push` is passed.

## Trigger publish (optional)

If `.github/workflows/publish.yml` (or similar) is wired to
`on: release: types: [published]`, create a GitHub release so the workflow
fires:

```bash
gh release create vX.Y.Z \
  --title "vX.Y.Z" \
  --notes "$(git log <previous-tag>..vX.Y.Z --format='- %s')"
```

Skip this step when `--no-release` is passed or when `gh` is not available.

## Report

Print the tag, the publish-workflow URL (if any), and the resolved version
string. For uv-dynamic-versioning, also run
`uv run python -c "import importlib.metadata as m; print(m.version('<pkg>'))"`
after the tag is created so the user can verify the version resolves
correctly.

## Notes

- Never amend or move an existing release tag — cut a new patch instead.
- Force-pushing the release branch (or moving a tag with `-f`) is destructive;
  refuse unless the user explicitly asks.

---
> Source: [Kakise/cartographing-kitties-plugin](https://github.com/Kakise/cartographing-kitties-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
