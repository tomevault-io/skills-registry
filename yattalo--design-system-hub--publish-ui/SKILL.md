---
name: publish-ui
description: Version and publish UI packages using changesets Use when this capability is needed.
metadata:
  author: yattalo
---

# Publish UI

Version and publish UI packages to npm registry using changesets.

## Parameters

- `bump` (optional): Version bump type (patch | minor | major, default: patch)
- `packages` (optional): Specific packages or "all" (default: "all")

## Usage

```
/publish-ui
/publish-ui --bump minor
/publish-ui --packages @crazyone/ui-vega,@crazyone/ui-nova
```

## Pre-flight Checklist

Before publishing, ensure:

- [ ] Working directory is clean (`git status`)
- [ ] All tests pass (`bun test`)
- [ ] Build succeeds (`bun run build`)
- [ ] No uncommitted changes

## Process

### Step 1: Create Changeset

Run the changeset wizard:

```bash
bun run changeset
```

This will prompt you to:
1. Select which packages have changed
2. Choose bump type for each (major/minor/patch)
3. Write a summary of changes

### Step 2: Review Changeset

A new file will be created in `.changeset/`:

```markdown
---
"@crazyone/ui-vega": patch
"@crazyone/ui-core": minor
---

Added new Button variants and fixed accessibility issues
```

### Step 3: Version Packages

Apply the changeset to update package versions:

```bash
bun run version
```

This will:
- Update `package.json` versions
- Update `CHANGELOG.md` files
- Remove the changeset file

### Step 4: Build Release

Ensure all packages build successfully:

```bash
bun run build
```

### Step 5: Commit Version Changes

```bash
git add .
git commit -m "chore: version packages"
```

### Step 6: Publish to Registry

```bash
bun run release
```

This runs `changeset publish` which:
- Publishes packages to npm
- Creates git tags for each release

### Step 7: Push Tags

```bash
git push --follow-tags
```

## Safety Guidelines

- Never force publish without review
- Always create git tags for releases
- Verify npm registry access before publishing
- Use `--dry-run` flag first for major releases
- Review changelog entries before committing

## Troubleshooting

### npm ERR! 403

- Check npm login: `npm whoami`
- Verify package scope access
- Ensure 2FA is configured if required

### Build Failures

- Run `bun install` to ensure dependencies
- Check for TypeScript errors: `bun run build`
- Verify workspace links are correct

### Changeset Not Found

- Run `bun run changeset` first
- Check `.changeset/` directory for pending changesets

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yattalo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
