---
name: version-iloom
description: Update version number and license date for iloom releases. Updates LICENSE and README.md dates, then runs pnpm version. Use when this capability is needed.
metadata:
  author: iloom-ai
---

# Version Iloom

Update the iloom version and license change date for a release.

## Arguments

Version bump type: `$ARGUMENTS` (defaults to `patch` if empty)

## License Date

The new license change date (4 years from today): !`date -v+4y "+%Y-%m-%d"`

## Task

1. **Update LICENSE file** (line 23): Change the `Change Date:` value to the license date shown above
2. **Update README.md** (line 527): Change the date in "Converts to Apache 2.0 on YYYY-MM-DD" to the license date shown above
3. **Commit date changes**: Stage and commit LICENSE and README.md with message "Update license change date to YYYY-MM-DD"
4. **Run version bump**: Execute `npm version $ARGUMENTS --no-git-tag-version` (use `patch` if no argument was provided), then commit the package.json change with the version number as the message
5. **Squash commits**: Run `git reset --soft HEAD~2 && git commit --no-verify -m "X.Y.Z"` to squash into one commit (use the new version number as the message)
6. **Create version tag**: Run `git tag vX.Y.Z` to create the tag on the squashed commit
7. **Report**: Show the old and new version numbers

## Important

- Use `npm version` with `--no-git-tag-version` to avoid creating the tag before squashing
- The date format must be `YYYY-MM-DD`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iloom-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
