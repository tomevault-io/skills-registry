---
name: gitbook-page-summary-maintenance
description: Create or update GitBook pages and keep SUMMARY.md synchronized for this repository. Use when adding a new docs page, moving or renaming pages, updating sidebar navigation, or enforcing AGENTS.md authoring standards (Markdown-only, one H1, semantic headings, relative links, and GitBook-friendly structure). Use when this capability is needed.
metadata:
  author: nexport-solutions
---

# GitBook Page + Summary Maintenance

Follow this workflow when making documentation changes in this repository.

## 1) Load local standards

1. Read `AGENTS.md` at repo root.
2. Treat `README.md` and `SUMMARY.md` as GitBook entry files.
3. Use Markdown only and relative links.
4. Follow GitBook content configuration rules from:
   `https://gitbook.com/docs/getting-started/git-sync/content-configuration`.

## 1.1) Git Sync configuration reminders

1. If `.gitbook.yaml` exists, respect `root` and `structure` paths.
2. Remember `structure.readme` and `structure.summary` are relative to `root`.
3. Do not create or edit README through GitBook UI when Git Sync is enabled; edit in Git only.
4. If files or folders move, add redirects in `.gitbook.yaml` (or site-level redirects where appropriate).

## 2) Create or update a page

1. Choose a clear file name in repo root or an existing docs folder.
2. Use exactly one H1 (`#`) for the page title.
3. Use semantic headings in order (`##`, `###`, `####`) without skipping levels.
4. Keep bullets concise and use meaningful link text.
5. Prefer callouts with blockquotes (`> Note`, `> Tip`, `> Warning`) when needed.

## 3) Update SUMMARY.md

1. Open `SUMMARY.md`.
2. Add or update the page link in the correct section.
3. Keep label text human-readable (Title Case where appropriate).
4. Keep links relative and point to `.md` targets.
5. Keep `SUMMARY.md` navigation-only; do not add inventory or scratch sections.
6. Do not reference the same markdown file twice in `SUMMARY.md`.
7. Use optional page link titles only when nav label should differ from page H1:
   `[Page title](path/page.md "Sidebar label")`.

## 4) Validate before finish

Run these checks from repo root:

```powershell
# Ensure SUMMARY links resolve
$root = (Get-Location).Path
$summary = Get-Content -Raw "$root\SUMMARY.md"
$links = [regex]::Matches($summary,'\((?:<)?([^)>]+\.md)(?:>)?\)') | ForEach-Object { $_.Groups[1].Value }
$missing = @()
foreach($l in $links){
  $p = Join-Path $root ($l -replace '/','\')
  if(-not (Test-Path $p)){ $missing += $l }
}
if($missing.Count -gt 0){ "Missing links:"; $missing } else { "SUMMARY links: OK" }
```

```powershell
# Quick duplicate filename signal (case-insensitive)
$root = (Get-Location).Path
Get-ChildItem -Recurse -File -Filter *.md |
  ForEach-Object { $_.FullName.Substring($root.Length + 1) } |
  Group-Object { $_.ToLower() } |
  Where-Object { $_.Count -gt 1 } |
  ForEach-Object { $_.Group -join ' | ' }
```

## 5) Commit hygiene

1. Keep diffs focused to the doc topic.
2. Include related `SUMMARY.md` updates in the same commit.
3. Avoid adding secrets, tokens, or internal-only tracker links.

## Output checklist

- Page content added or updated.
- `SUMMARY.md` updated in same change.
- All summary links resolved.
- Heading hierarchy and link style aligned with `AGENTS.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nexport-solutions) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
