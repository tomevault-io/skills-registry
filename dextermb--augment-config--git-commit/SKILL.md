---
name: git-commit
description: Instructions and workflow steps to create a Git commit message Use when this capability is needed.
metadata:
  author: dextermb
---

When being asked to create a Git commit message achieve the following contraints:
- The first message should be prefixed with the current branch
  - Followed by a dash
  - Followed by a summary of the changes about to be committed
    - A summary should be less than 125 characters long
- The second message should be a more detailed summary of the changes about to be committed
  - List each file about to be changed
    - A file should be the relative filepath from the root of the directory
    - Leave a blank new line between each file and the previous summary
    - Summarize the changes per file
      - A summary should be less than 255 characters long

IMPORTANT: When providing summaries make sure to check for:
- new uses of files
- read the actual content changed
- when summarizing changes do not use past-tense (e.g. "Added") use present-tense (e.g. "Add")
- if there are no staged files then stage all changes

An example command would be something such as:

```bash
git commit -m '<branch> - <overall summary>' -m EOF
- <file>
  <file summary>

- <file 2>
  <file 2 summary>
EOF
```

Placeholders to be replaced:
- "<branch>": The current branch
- "<overall summary>: Summary of the changes to be committed
- "<file>": A relative file path per each file changed followed by...
- "<file summary>": A summary per file of changes made

If you are unsure of how the command should be structured read the `git commit` documentation found at
> https://git-scm.com/docs/git-commit

IMPORTANT: Before committing the changes you must confirm with me. Provide a preview of what the commit messages look like so I can approve.

Once approved you can push your commit.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dextermb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
