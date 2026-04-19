---
name: gh-pages
description: Create GitHub repo and enable GitHub Pages for static site hosting Use when this capability is needed.
metadata:
  author: tooyakov-art
---

# Setup GitHub Pages

Create a new GitHub repository and enable GitHub Pages:

1. **Create repo:**
```bash
gh repo create $ARGUMENTS --public --source=. --push
```

2. **Enable GitHub Pages:**
```bash
gh api repos/OWNER/$ARGUMENTS/pages -X POST -f source='{"branch":"master","path":"/"}'
```

3. **Get the Pages URL:**
```bash
gh api repos/OWNER/$ARGUMENTS/pages --jq '.html_url'
```

The site will be available at: `https://OWNER.github.io/$ARGUMENTS/`

Note: Replace OWNER with actual GitHub username.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tooyakov-art) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
