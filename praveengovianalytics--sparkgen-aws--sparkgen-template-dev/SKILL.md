---
name: sparkgen-template-dev
description: Develop and modify the SparkGen-AWS cookiecutter template — variables, hooks, files Use when this capability is needed.
metadata:
  author: praveengovianalytics
---

# SparkGen Template Dev

Guide template development for the SparkGen-AWS cookiecutter.

## Dynamic Context

Before any action:
1. Read `cookiecutter.json` for current variables
2. Read `hooks/post_gen_project.py` for post-generation logic
3. List files in `{{cookiecutter.project_slug}}/` for template structure

## Actions

### Add Variable (`/sparkgen-template-dev add-variable <name> <default> [choices]`)
1. Add the variable to `cookiecutter.json`:
   - Simple value: `"<name>": "<default>"`
   - Choice list: `"<name>": ["<default>", "<option2>", ...]`
   - Add a section comment if it's a new category: `"_<section>_section": "=== Section Name ==="`
2. Use the variable in template files: `{{ cookiecutter.<name> }}`
3. If the variable controls file inclusion, update `hooks/post_gen_project.py`
4. Run matrix test: `bash test_cookiecutter_matrix.sh`

### Add Hook Logic (`/sparkgen-template-dev add-hook <description>`)
Add conditional logic to `hooks/post_gen_project.py`:
- Use `remove_file(path)` to remove files based on cookiecutter choices
- Use `remove_dir(path)` to remove directories
- Pattern: `if "{{ cookiecutter.var_name }}" == "value": remove_file/dir(...)`

### Update Template (`/sparkgen-template-dev update-template <file-path>`)
When editing files inside `{{cookiecutter.project_slug}}/`:

**CRITICAL: Jinja2 Escaping Rules**
- `{{ cookiecutter.* }}` — renders the cookiecutter variable (DO NOT wrap)
- `{{ any_other_var }}` — MUST wrap in `{% raw %}...{% endraw %}`
- `${{ github.* }}` in GitHub Actions — MUST wrap in `{% raw %}...{% endraw %}`
- Runtime Jinja2 templates (prompts, etc.) — MUST wrap in `{% raw %}...{% endraw %}`

Examples:
```
# Cookiecutter var (renders at generation time) — NO wrapping
provider: "{{ cookiecutter.llm_provider }}"

# Runtime Jinja2 var (renders at runtime) — MUST wrap
{% raw %}{{ context }}{% endraw %}

# GitHub Actions (renders in CI) — MUST wrap
{% raw %}${{ secrets.AWS_ACCESS_KEY }}{% endraw %}
```

### Validate (`/sparkgen-template-dev validate`)
Run full validation:
1. `python -c "import json; json.load(open('cookiecutter.json'))"` — JSON syntax
2. `bash test_cookiecutter_matrix.sh` — generate + compile all combos
3. Check for unescaped `{{ }}` in template files that aren't cookiecutter vars:
   Search for `{{ ` in template files and verify each is either a cookiecutter var or wrapped in raw tags

## Template Structure Reference
```
cookiecutter.json                    ← 93+ variables with defaults
hooks/post_gen_project.py           ← Conditional file removal
{{cookiecutter.project_slug}}/      ← Everything here is processed by Jinja2
  app/                              ← Python source (api.py, mcp_server.py, etc.)
  config/ai_workflow.yaml           ← Uses cookiecutter vars + {% raw %} for runtime vars
  .github/workflows/                ← Uses {% raw %} for ${{ }} expressions
  prompts/, contexts/, guardrails/  ← Markdown files (may use {% raw %})
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/praveengovianalytics) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
