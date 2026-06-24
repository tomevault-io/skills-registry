---
name: new-python-project
description: Scaffold a new Python package using the SSC (Scientific Software Center, Heidelberg University) cookiecutter template at github.com/ssciwr/cookiecutter-python-package. Use when the user wants to create, scaffold, or start a new Python project, especially for scientific software. Use when this capability is needed.
metadata:
  author: lkeegan
---

# Create a new SSC Python project

Use this skill when the user wants to create a new Python package and either explicitly mentions the SSC cookiecutter, scientific Python conventions, or has no strong preference. If the user has no strong preference, briefly mention this template is geared toward SSC-Heidelberg conventions before proceeding.

The skill wraps [`ssciwr/cookiecutter-python-package`](https://github.com/ssciwr/cookiecutter-python-package) and runs it non-interactively.

## Gather inputs

First, extract any values the user already gave. Then ask at most one follow-up question before running cookiecutter. Do not prompt one variable at a time, and do not ask a separate "which features do you want?" question unless the user explicitly asked to customize features.

Required before running:

- `project_name` - human-readable name, e.g. `"My Analysis Tool"`
- `full_name` - author name

Optional values and defaults:

- `parent_directory` - current working directory, unless the user asked for a different location
- `remote_url` - git remote URL, e.g. `https://github.com/user/repo`; default `None`
- `license` - one of `MIT`, `BSD-2`, `GPL-3.0`, `LGPL-3.0`, `None`; default `MIT`
- `github_actions_ci` - `Yes` if `remote_url` is a GitHub URL, otherwise `No`
- `gitlab_ci` - `Yes` if `remote_url` is a GitLab URL, otherwise `No`
- `notebooks` - default `No`
- `commandlineinterface` - default `No`
- `version_management` - default `setuptools_scm`

`project_slug` is auto-derived from `remote_url` or `project_name`. Do not ask for it.

If any required value is missing, ask one concise question like:

```text
I can create this with the SSC Python cookiecutter. Please provide:
- project name:
- author name:
- remote URL, or "None" (default: None):

I will create it in the current directory and use defaults for the remaining options: MIT license, CI inferred from the remote URL, no notebooks, no CLI, setuptools_scm versioning.
```

If the user answers with only the required values or says to use defaults, proceed with the defaults above. Do not ask again for optional values.

## Run the cookiecutter

Before calling any tool, verify every placeholder in the command below has a concrete value. Never call a tool with `<value>`, `<value-or-None>`, `<Yes|No>`, or any other placeholder still present.

Run the command from `parent_directory`; by default this is the current working directory. The cookiecutter creates a new subdirectory there.

```bash
uvx cookiecutter --no-input gh:ssciwr/cookiecutter-python-package \
  project_name="<value>" \
  remote_url="<value-or-None>" \
  full_name="<value>" \
  license=<value> \
  github_actions_ci=<Yes|No> \
  gitlab_ci=<Yes|No> \
  notebooks=<Yes|No> \
  commandlineinterface=<No|Yes> \
  version_management=<manually|setuptools_scm>
```

If `uvx` is unavailable, fall back to `pipx run cookiecutter ...` or `python -m cookiecutter ...` (requires `cookiecutter` installed).

## After it runs

The cookiecutter's post-generation hook automatically:

- runs `git init` and creates an initial commit tagged `v0.0.1`
- adds `remote_url` as the `origin` remote if one was provided
- runs `pre-commit run -a` once if pre-commit is configured

Report the generated directory path to the user and point them at `FILESTRUCTURE.md` and `TODO.md` inside it. Do **not** attempt `git push` - the user must ensure the remote repository exists first.

---
> Source: [lkeegan/skills](https://github.com/lkeegan/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
