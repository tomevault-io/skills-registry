---
name: gitlab-review
description: Address the GitLab merge request review comments in $1 Use when this capability is needed.
metadata:
  author: ktdreyer
---

Address the GitLab merge request review comments in $1

Use gitlab tools to query the GitLab MR and read the review discussion comments. Take note of the branch name that has my in-progress changes.

Ask me before you push any code changes.

When writing the commit message, add a "Reported-By:" line to credit the reviewer. For example: "Reported-By: John Smith <jsmith@example.com>". To determine the user's preferred email address, look in the Git history for this repository and use that.

Draft a response to any MR comments, but do not post it. Keep it very brief, professional, and friendly. Ask me if I want to post the comment(s) and resolve the discussion thread(s).

Before you edit the code, pull in the very latest changes from the "main" branch in the "origin" remote. For example:

```
git fetch origin
# On feature branch:
git rebase origin/main
```

This will ensure that you are editing the very latest version of the code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ktdreyer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
