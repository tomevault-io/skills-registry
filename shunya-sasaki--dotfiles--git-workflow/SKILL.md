---
name: git-workflow
description: When you're asked to work with Git, you MUST use this skill. Use when this capability is needed.
metadata:
  author: shunya-sasaki
---

# Git Workflow

This skill provides instructions for working with Git repositories and their
forges. Use `git` for local operations and `gf` for forge operations (issues,
pull requests, labels) and worktree management. `gf` auto-detects the forge
(GitHub or Gitea), so the same commands work regardless of the remote.

Tasks described in this skill:

- Generate Commit Message
- Create Issue
- Create Pull Request
- Create Worktree
- Remove Worktree
- Resolve the Issue

## Common arguments

`gf` commands share these arguments:

- `<title>`: Title of an issue or pull request. Must be concise (max 50
  characters) and follow the **Conventional Commit** style,
  `<type>(<scope>): <description>` or `<type>: <description>`, with a lowercase
  description in the imperative mood.
  e.g. `feat(doc): add issue creation procedure`
- `<type>`: One of the following:
  - `feat`: A new feature
  - `fix`: A bug fix
  - `docs`: Documentation only changes
  - `style`: Changes that do not affect meaning (formatting, white-space, etc.)
  - `refactor`: A code change that neither fixes a bug nor adds a feature
  - `perf`: A code change that improves performance
  - `test`: Adding or correcting tests
  - `chore`: Auxiliary tools, e.g. documentation generation
  - `build`: Changes to the build system or external dependencies
  - `ci`: Changes to CI configuration files and scripts
- `<scope>`: Optional area of the change, e.g. `doc`, `api`, `workflow`.
- `<body>`: Body of an issue or pull request, composed from its template.
- `<label>`: Label name. Get the available labels with `gf label list`.
- `<target_branch>`: Base branch of a pull request. Defaults to `main`.
- `<number>`: Issue or pull request number.
- `<branch_name>`: Name of the branch to work on, following the **Conventional
  Commit** style or the issue tag, e.g. `feat/add-login`.

### Strict Rules

- The title and body MUST NOT contain any ANSI escape sequences, terminal
  control codes, or other non-printable characters, including:
  - SGR / color codes such as `\x1b[31m`, `\033[0m`, `[1;32m`
  - Cursor and screen codes such as `\x1b[2k`, `\x1b[?25l`, `\x1b[H`
  - Any byte in the ranges `\x00`-`\x08`, `\x0B`-`\x1F`, or `\x7F`
- If any command output you read contains such sequences, strip them before
  including the content. After composing the body, scan it once more and remove
  any remaining bytes that match `\x1B\[`, `\x1B\]`, or other C0/C1 control
  characters.

## Generate Commit Message

### Procedure

1. Run `git --no-pager diff --cached --no-color` to get staged changes.
2. Based on the staged changes, generate a commit message following the
   **Conventional Commit** style (see [Common arguments](#common-arguments)).
3. Output ONLY the raw commit message with no code blocks, explanations,
   or fillers.

## Create Issue

IF you're asked to create an issue, THEN you MUST work on this task.

### Procedure

1. Understand the user's request, problem report, or improvement idea.
2. If the repository context is needed, inspect relevant files to understand the
   current implementation, configuration, documentation, or behavior.
3. Create the `<title>` using the **Conventional Commit** style.
4. Get the labels with `gf label list` and select a `<label>` from the list.
   If the list is empty, there is no label.
5. Get the body template with `gf issue template --label <label>`.
6. Compose the `<body>` following the template.
7. Run `gf issue create --title <title> --body <body> --label <label>`.

## Create Pull Request

IF you're asked to create a pull request (PR), THEN you MUST work on this task.

### Procedure

1. Run `git --no-pager diff --no-color <target_branch>...HEAD` to get the
   changes between the current branch and the target branch.
2. Create the `<title>` using the **Conventional Commit** style.
3. Get the body template with `gf pr template`.
4. Compose the `<body>` following the template.
5. Run `gf pr create --title <title> --body <body> -B <target_branch> --label <label>`.

## Create Worktree

When you're required to create a worktree or an isolated copy to edit files,
create one with `gf worktree create`.

### Procedure

1. Analyze the user request to understand your task.
2. Determine the `<branch_name>`. Use the tag the user specified; otherwise
   derive it from the request.
3. Run `gf worktree create -b <branch_name>`. It creates the worktree at
   `~/.tmp/<repository-name>_<branch-name>` (the repository name is detected
   from the remote) and prints the path. Work in that directory.

### Notes

- `gf` reuses the branch if it already exists; otherwise it creates a new one.
- `gf` places the worktree under `~/.tmp/`, never in the project directory.

## Remove Worktree

When you're requested to remove a worktree, use this skill.

### Procedure

1. Run `gf worktree delete -b <branch_name>` to remove the worktree.

### Notes

- This removes only the worktree directory; the branch is kept.

## Resolve the Issue

When you're required to resolve an issue or something to edit files in the
project, use this skill and follow the instructed procedure.

### Procedure

1. Analyze the user request.
2. If the issue number is specified, view it with `gf issue view <number>`.
   Otherwise list issues with `gf issue list` and ask the user which to resolve.
3. If no issue matches the user request, create a new issue
   (see [Create Issue](#create-issue)).
4. Create a worktree and branch for the issue
   (see [Create Worktree](#create-worktree)).
5. Edit files in the worktree to resolve the issue.
6. Commit your changes (see [Generate Commit Message](#generate-commit-message))
   and push the branch to the remote repository.
7. Create a pull request linked to the issue
   (see [Create Pull Request](#create-pull-request)).
8. Remove the worktree (see [Remove Worktree](#remove-worktree)).

### Strict Rules

- Do NOT work on the current branch directly.
  Always create a new branch for your work
  and create a pull request to merge it into the current branch
  or the user specified branch.

---
> Source: [shunya-sasaki/dotfiles](https://github.com/shunya-sasaki/dotfiles) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
