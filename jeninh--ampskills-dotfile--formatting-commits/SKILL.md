---
name: formatting-commits
description: Creates commits strictly following Conventional Commits format. ALWAYS read BEFORE committing changes, amending commits, or when the user mentions git commits, conventional commits, or commit messages.
compatibility: Requires `git` and `formatted-commit` CLI tools
license: AGPL-3.0-or-later
metadata:
  author: Amolith <amolith@secluded.site>
---

Create commits using `formatted-commit`. For amends, choose the appropriate approach:

- **Message stays accurate** → `git commit --amend --no-edit` (or `-a --amend --no-edit` to stage all)
- **Message needs updating** → `formatted-commit --amend` to reconstruct with new type/scope/body

`formatted-commit` has no sub-commands and the following options:

<formatted-commit_flags>
-t --type Commit type (required)
-s --scope Commit scope (optional)
-B --breaking Mark as breaking change (optional)
-m --message Commit message (required)
-b --body Commit body (optional)
-T --trailer Trailer in 'Sentence-case-key: value' format (optional, repeatable)
-a --add Stage all modified files before committing (optional)
--amend Amend the previous commit (optional)
-h --help
</formatted-commit_flags>
<formatted-commit_example>
formatted-commit -t feat -s "web/git-bug" -m "do a fancy new thing" -b "$(cat <<'EOF'
Multi-line

- Body
- Here

EOF
)" -T "Assisted-by: [Model] via [Agent]"
</formatted-commit_example>

Most source code repositories require both an appropriate prefix _and_ scope. Necessity of scope increases with repository size; the smaller the repo, the less necessary the scope. Valid trailers for ticket tracking integration depend on the platform in use.

- GitHub
  - Closes:
  - Fixes:
  - Resolves:
  - References:
- SourceHut
  - Closes:
  - Fixes:
  - Implements:
  - References:

Refer to [installing-formatted-commit.md](references/installing-formatted-commit.md) if it's unavailable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeninh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
