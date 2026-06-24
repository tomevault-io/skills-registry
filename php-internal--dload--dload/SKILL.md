---
name: dload-fetch-tool
description: Get a CLI tool — native binary or PHAR — from a GitHub release into a project folder, using dload as the fetcher. Use when the user says anything like "I need binary X in this project", "download tool Y", "pull a PHAR", "install rr / temporal / buf locally", "set up CLI tooling for this repo", "add X to dload", or asks to troubleshoot why a download isn't picking up the right asset. Assumes dload is (or can be) installed via Composer (`vendor/bin/dload`). Covers both paths — quick `<download>` for tools dload already knows, and a full inline `<registry>` definition in `dload.xml` for tools it doesn't, including the PHAR-specific shape. Use when this capability is needed.
metadata:
  author: php-internal
---

# Download a GitHub binary or PHAR into a project with dload

The whole task — "I want tool X from GitHub available in this project's folder" — boils down to one decision: **does dload already know this tool?** If yes, you add one line to `dload.xml`. If no, you also describe the tool inline in the same file. The artefact can be a native binary or a PHP `.phar` — both are first-class, they differ only in a couple of fields.

> This skill targets **consumers** of dload — projects that pulled it in via `composer require internal/dload`. Everything happens in the project's own `dload.xml`. You never edit anything inside `vendor/`.

## Decision tree

```
Does `vendor/bin/dload software` list the tool?
├── Yes → add <download software="alias" /> to dload.xml.   (Stop.)
└── No  → describe it inline in dload.xml's <registry>,
         then add <download software="alias" /> referencing it.
```

If the download fails after that, jump to the **Troubleshooting** section — it walks the four-stage selection pipeline.

## Step 1 — check what dload already knows

```bash
./vendor/bin/dload software
```

This prints every tool baked into dload's built-in registry with its alias and description. If your tool is there, skip to Step 3.

## Step 2 — describe a missing tool inline in `dload.xml`

You teach dload about a new tool by adding a `<software>` block to the `<registry>` element of *your project's* `dload.xml`. The built-in registry stays untouched; `overwrite="false"` (the default) merges your entry with what dload already ships.

You need six facts before writing the block:

1. **GitHub repo** as `owner/repo`.
2. **A real release tag** to inspect — any recent one will do.
3. **The asset filename matrix** — fetch and inspect:
   ```bash
   curl -s https://api.github.com/repos/<owner>/<repo>/releases/tags/<tag> \
     | grep -oE '"name": "[^"]+"'
   ```
4. **Sibling tools in the same release that must be excluded** — e.g. `bufbuild/buf` ships `buf-*` AND `protoc-gen-buf-breaking-*` / `protoc-gen-buf-lint-*`; only `buf-*` is wanted.
5. **The version command** — usually `--version`; some tools use `version` (no dashes) or have none.
6. **Artefact type** — native binary (executable for a specific OS/arch) or PHAR (single `.phar` file, platform-independent).

### Designing `asset-pattern`

This regex filters the release's asset list. dload then runs OS/arch detection on every matching filename to pick the right one for the host — so your regex does **not** need to encode the platform. It just needs to:

- Match all OS/arch variants of the desired tool.
- Exclude unrelated assets (sibling tools, checksums, signatures, source archives).

Tokens dload's OS/arch matchers recognise (case-insensitive, bounded by `_` or word boundary):

| Kind | Tokens |
|---|---|
| OS   | `windows`, `linux`, `darwin`, `macos`, `alpine`, `bsd`, `freebsd`, `win32`, `win64` |
| Arch | `amd64`, `arm64`, `aarch64`, `x86_64`, `x64`, `win64` |

Typical asset-pattern shapes:

| Situation | Pattern |
|---|---|
| Single-name tool whose assets share a prefix | `/^<name>-.*/` (e.g. `/^buf-.*/`, `/^roadrunner-.*/`) |
| Tool with no stable prefix on assets | Anchor on something stable, e.g. `/^artifacts.*/` for `buggregator/frontend` |
| Multiple tools in one release | Tighten to exclude siblings (use `-` after the name to anchor, not just `.*`) |
| PHAR | `/^<name>\.phar$/` or `/^.*\.phar$/` |

### Designing `binary.pattern`

This regex matches the **executable on disk after the asset is downloaded/extracted**. For native binaries, three cases:

| Asset type | What ends up on disk | Pattern |
|---|---|---|
| Raw binary (no archive) | The asset filename itself | `/^<name>(-.*)?(\.exe)?$/` |
| Archive with `bin/<name>` inside | Plain `<name>` or `<name>.exe` | `/^<name>(\.exe)?$/` |
| Mixed (both raw and archive published) | Either form | `/^<name>(-.*)?(\.exe)?$/` |

When unsure, the broad form (`/^<name>(-.*)?(\.exe)?$/`) is safe. `binary.pattern` is **optional** — drop it when `binary.name` alone uniquely identifies the file (e.g. a tarball with exactly one `tool` file).

If the user-facing CLI name differs from the filename (RoadRunner ships as `roadrunner`, invoked as `rr`), set `binary.name` to the short alias and use the pattern to match both.

For PHAR, `binary.pattern` is usually `/^.*\.phar$/` or omitted.

### Inline `<registry>` — native binary

```xml
<registry overwrite="false">
    <software name="MyTool" alias="mytool" description="…"
              homepage="https://example.com/">
        <repository type="github" uri="owner/repo" asset-pattern="/^mytool-.*/" />
        <binary name="mytool" pattern="/^mytool(-.*)?(\.exe)?$/" version-command="--version" />
    </software>
</registry>
```

In XML attributes regex backslashes are literal — write `\.exe`, not `\\.exe`.

`overwrite="false"` (default) merges your entries with the built-in registry. `overwrite="true"` *replaces* it — use only when you want exactly that.

### Inline `<registry>` — PHAR

A PHAR is one platform-independent `.phar` file — no extraction, no OS/arch matching. The shape is the same as a binary entry but simpler:

```xml
<software name="Trap" alias="trap" description="…" homepage="https://buggregator.dev/">
    <repository type="github" uri="buggregator/trap" asset-pattern="/^trap\.phar$/" />
    <binary name="trap" pattern="/^.*\.phar$/" version-command="--version" />
</software>
```

On the `<download>` side, always set `type="phar"` so dload skips archive-extraction logic (see Step 3).

### Other non-binary assets

Frontend bundles, configs, anything that isn't executable — use the `<file>` element instead of `<binary>` in the inline definition, and `type="archive"` on `<download>`.

## Step 3 — add the `<download>` action

Wire the tool into your `dload.xml`:

```xml
<?xml version="1.0"?>
<dload
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:noNamespaceSchemaLocation="https://raw.githubusercontent.com/php-internal/dload/refs/heads/main/dload.xsd"
    temp-dir="./runtime"
>
    <actions>
        <download software="buf" />
        <download software="trap" type="phar" version="^1.1" />
    </actions>
    <registry overwrite="false">
        <!-- inline <software> blocks from Step 2, if any -->
    </registry>
</dload>
```

Useful `<download>` attributes:

| Attribute | Purpose |
|---|---|
| `software` | Alias or name of the tool (built-in or inline). Required. |
| `version` | Composer-style constraint: `^2025.1`, `~1.0.0`, `^2.12.0-feature`, `^2.12.0@beta`, `^2.12.0-hotfix@rc`. Omit for latest stable. |
| `version-path` | Pull version from a JSON file: `composer.json@require.temporal/sdk`. |
| `extract-path` | Override target directory (default: project root). |
| `type` | `binary` (default for executables), `archive`, `phar`, `velox`. Set when the tool isn't a plain executable — required for PHAR. |

Stability suffixes (`@alpha`, `@beta`, `@RC`, `@stable`) follow Composer's ordering, with `stable` as the default.

## Step 4 — pick the destination folder

By default dload places the artefact at the project root. To target a specific folder use `extract-path` on `<download>`:

```xml
<actions>
    <download software="rr" extract-path="./bin" />
    <download software="trap" type="phar" extract-path="./tools" />
</actions>
```

Same rule for binary and PHAR — `extract-path` is the literal folder where the file lands. Create the folder ahead of time if your VCS doesn't track empty ones (a `.gitkeep` is the usual trick), and add the downloaded file itself to `.gitignore` — only `dload.xml` belongs in git.

## Step 5 — run dload

```bash
./vendor/bin/dload get             # process all <download> entries in dload.xml
./vendor/bin/dload get <alias>     # one specific entry
```

Verify it runs (`./bin/rr --version`, `php ./tools/trap.phar --version`) before considering the task done.

## Troubleshooting — when dload can't find an asset

Failures almost always belong to one of four stages. Walk them in order — fixing an earlier stage often makes later symptoms disappear.

### Stage 1 — release selection

Confirm the tag you expect is actually visible and matches the constraint:

```bash
curl -s "https://api.github.com/repos/<owner>/<repo>/releases?per_page=20" \
  | grep -E '"(tag_name|prerelease|draft)"'
```

- Release is `prerelease: true` but `version` demands stable → lower the stability (`@beta`/`@RC`/`@alpha`).
- Release is `draft: true` → not visible to the API yet.
- `version-path` resolved an unexpected constraint → echo the resolved value and re-check.

### Stage 2 — asset filtering

Fetch the assets for the target tag (command from Step 2) and mentally apply your `asset-pattern`.

- Pattern not slash-delimited (`^...` instead of `/^.../`) → silently fails.
- In XML attributes backslashes are literal — `\.exe`, not `\\.exe`.
- Broad pattern accidentally catches sibling tools / checksums / signatures → tighten.

### Stage 3 — platform matching

After `asset-pattern` filters, dload uses these regexes to pick the host's variant:

- OS regex: `/(?:\b|_)(windows|linux|darwin|macos|alpine|bsd|freebsd|win32|win64)(?:\b|_)/i`
- Arch regex: `/(?:\b|_)(amd64|arm64|aarch64|x86_64|x64|win64)(?:\b|_)/i`

If neither matches a filtered candidate, it's discarded. Common offenders:

- Non-standard tokens: `linux64` (no word boundary before `64`), `osx`, `darwin_universal` — won't match.
- No arch separator: `tool-linux64.tar.gz` — OS matches via the trailing boundary but no arch token is extractable.

### Stage 4 — binary extraction

After the chosen asset is unpacked into `temp-dir`, `binary.pattern` selects the executable.

- Pattern missing → falls back to exact match on `binary.name`. If the archive nests the binary, add a pattern.
- Pattern over-matches (picks README, LICENSE) → tighten.
- Pattern under-matches → inspect what's actually inside:
  ```bash
  tar -tzf path/to/asset.tar.gz       # or: unzip -l path/to/asset.zip
  ```
  Then update the pattern. Use `/^<name>(-.*)?(\.exe)?$/` for mixed raw-vs-archive distributions.

### Triage one-liner

```bash
OWNER=bufbuild; REPO=buf; TAG=v1.69.0
echo "=== Assets ==="
curl -s "https://api.github.com/repos/$OWNER/$REPO/releases/tags/$TAG" \
  | grep -oE '"name": "[^"]+"' | sort -u
echo "=== Host ==="
php -r "echo PHP_OS_FAMILY, ' / ', php_uname('m'), PHP_EOL;"
```

Compare the asset names with the patterns in your registry entry and the OS/arch tokens above.

## Reference

- Authoritative XML schema: `https://raw.githubusercontent.com/php-internal/dload/refs/heads/main/dload.xsd` (also in your `vendor/internal/dload/dload.xsd`).
- Working example with comments: `vendor/internal/dload/dload-example.xml`.
- List of built-in tools and aliases: `./vendor/bin/dload software`.

---
> Source: [php-internal/dload](https://github.com/php-internal/dload) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
