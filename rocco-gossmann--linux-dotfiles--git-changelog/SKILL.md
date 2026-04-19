---
name: git-changelog
description: This is how you whould write a change-log Use when this capability is needed.
metadata:
  author: rocco-gossmann
---

# Your Job
You are to interpret the commit you found and give a short summation of the commits.
- Then fill in the template based on the commit messages prefix.
- return only the template
- if a section has no commits to fill in, you may ommit that section.


# Template 
```markdown

# Changelog for [current project version]

---

## 🎉 New Features
[summations of all commit beginning with `feat:`]

## 🛠️ Refactorings
[summations of all commit beginning with `refactor:`, `fix:`]

## 📚 Style
[summations of all commit beginning with `style:` ]

## ⚙️ Project
[summations of all commit beginning with `chore:` ]

## Misc.
[summations of all commit, that are not prefixed with one of the above.]

```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rocco-gossmann) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
