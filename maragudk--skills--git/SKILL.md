---
name: git
description: Guide for using git according to my preferences. Use it when you're asked to commit something. Use when this capability is needed.
metadata:
  author: maragudk
---

# git

Most of git usage is what you already know, so depend on that. This is skill is just a refinement.

## Branch naming

Just name the branch a short sentence seperated with dashes. Example: `add-some-feature`. Don't use `feat/`, `hotfix/` etc. prefixes.

## Commit messages

- Always enclose code identifiers with backticks. Example: "Add `html.UserPage` component"
- Always refer to Go code identifiers including the package name, like in `html.UserPage` above. Fields and methods on structs can be referred with `model.User.Name`.
- Ask me about any Github issues that should be referenced. Reference them at the end of the commit message like this: "See #123, #234". If the commit fixes one or more issues, use "Fixes #123, fixes #234" instead (the double "fixes" is important for Github to actually close the issue).
- Don't mention that you've updated tests, that's assumed.

## Committing

- Don't amend previous commits unless instructed to. When committing after the first commit on a branch, just commit with a simple message (e.g. "fixing …"), because the branch will most times be squashed on Github anyway.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maragudk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
