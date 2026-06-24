---
name: adding-github-actions-pypi-release
description: Adds GitHub Actions workflows for publishing Python packages to PyPI and TestPyPI. Use when the user mentions PyPI publishing, GitHub Actions, package deployment, CI/CD automation, or Python package release. Use when this capability is needed.
metadata:
  author: yeyuan98
---

## Purpose

Automate the creation of GitHub Actions workflows for publishing Python packages to PyPI (production) and TestPyPI (testing). This skill:
- Creates a `release.yml` workflow that triggers on GitHub Releases to publish to PyPI
- Creates a `publish-testpypi.yml` workflow that triggers on commits to `main` branch that modify `pyproject.toml` to publish to TestPyPI

Follow steps below exactly. If the procedure below didn't work out. Do not try to resolve issues on your own. Message the user that which step errored and stop.

**Step 01: Verify current working directory is a Python package**

Run bash: `git --version`
- If not found, message user "This is not a git repository. GitHub Actions require a git repository." and stop.

Run bash: `ls pyproject.toml setup.py 2>/dev/null`
- If neither exists, message user "Not a Python package (no pyproject.toml or setup.py found)." and stop.

Run bash: `ls .github/workflows/*.yml 2>/dev/null`
- If any .yml files exist, list them and ask user "Existing GitHub Actions workflows found. Continue?" using AskUserQuestion.

**Step 02: Inspect existing GitHub Actions**

Check for conflicting workflow files that would prevent the new workflows from working:

Run bash: `ls .github/workflows/release.yml .github/workflows/publish-testpypi.yml 2>/dev/null`
- If either file exists, message user "Conflicting workflow file(s) found: [list]. Please remove or rename them before running this skill." and stop.

Check if .github/workflows directory exists:
Run bash: `ls -d .github/workflows 2>/dev/null`
- If directory doesn't exist, create it with: `mkdir -p .github/workflows`

**Step 03: AskUserQuestion to gather package release information**

First, derive the PyPI package name from pyproject.toml:

Run bash: `grep "^name = " pyproject.toml | head -1`
- Extract the package name from the output (format: `name = "package-name"`)
- Record the package name for use in the workflow templates

Use AskUserQuestion with the following questions:

1. **Python version**: "Which Python version should the workflows use?"
   - Options: "3.12 (Recommended)", "3.11", "3.13", "3.10"

2. **PyPI/TestPyPI publishing setup**: "Have you set up trusted publishing on PyPI and TestPyPI?"
   - Options: "Yes, both are configured", "Only PyPI is configured", "Only TestPyPI is configured", "No, neither is configured"
   - If user answers "No, neither is configured", message user "Please set up trusted publishing before continuing:

   For PyPI: Go to https://pypi.org/manage/account/publishing/
   For TestPyPI: Go to https://test.pypi.org/manage/account/publishing/

   Follow the instructions to add your GitHub repository as a trusted publisher." and stop.

3. **Main branch name**: "What is your main branch name?"
   - Options: "main (Recommended)", "master", "develop"
   - This is used for the TestPyPI workflow trigger

Record these answers for use in the workflow templates.

**Step 04: Create GitHub Actions workflow files**

Create two workflow files using the templates in the templates/ directory:

**File 1: .github/workflows/release.yml**

Read the template from `./templates/release.yml`
Replace the following placeholders:
- `{{PYTHON_VERSION}}` → user's chosen Python version
- `{{PACKAGE_NAME}}` → user's PyPI package name

Write the file to `.github/workflows/release.yml`

**File 2: .github/workflows/publish-testpypi.yml**

Read the template from `./templates/publish-testpypi.yml`
Replace the following placeholders:
- `{{PYTHON_VERSION}}` → user's chosen Python version
- `{{PACKAGE_NAME}}` → user's PyPI package name
- `{{MAIN_BRANCH}}` → user's main branch name

Write the file to `.github/workflows/publish-testpypi.yml`

**Step 05: Provide feedback to user**

Message the user with the following information:

"✅ GitHub Actions workflows created successfully!

Created files:
- `.github/workflows/release.yml` - Publishes to PyPI on GitHub Release
- `.github/workflows/publish-testpypi.yml` - Publishes to TestPyPI when pyproject.toml changes on {{MAIN_BRANCH}}

**Next Steps:**

1. **Set up PyPI environment** (for release.yml):
   - Go to: https://github.com/{{REPO}}/settings/environments/new
   - Create environment named `pypi`
   - Add environment variable: `url` = `https://pypi.org/p/{{PACKAGE_NAME}}`

2. **Set up TestPyPI environment** (for publish-testpypi.yml):
   - Create environment named `testpypi`
   - Add environment variable: `url` = `https://test.pypi.org/p/{{PACKAGE_NAME}}`

3. **Configure PyPI publishing** (uses trusted publishing):
   - For PyPI: https://pypi.org/manage/account/publishing/
   - For TestPyPI: https://test.pypi.org/manage/account/publishing/
   - Add your GitHub repository as a trusted publisher

4. **Test the workflows**:
   - TestPyPI: Make a commit that changes pyproject.toml and push to {{MAIN_BRANCH}}
   - PyPI: Create a GitHub Release (draft or pre-release for testing)"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yeyuan98) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
