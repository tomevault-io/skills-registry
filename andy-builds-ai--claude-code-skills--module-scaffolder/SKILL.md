---
name: module-scaffolder
description: Sets up the skeleton of a new code project - adapts to the project type (Python module, CLI tool, API service, learning script). Provides directory structure, .gitignore, README stub, requirements.txt base. Triggers when a new project starts.
metadata:
  author: andy-builds-ai
---


# module-scaffolder

Sets up the skeleton for a new code project. Triggers when the user starts a new project - whatever the type.

## When this skill triggers

When the user creates a new folder for a project or says "I'm starting a new project". Run it before the first code, not after.

## Four project types

Each type has its own skeleton. Ask the user which type it is first, if it isn't clear from the context.

1. **Python module** - full standard for a reusable library
2. **CLI tool** - command-line tool with an entry point
3. **API service** - web service with endpoints and secrets
4. **Learning script** (own exercises) - minimal

## 1. Python module

Full standard. When the user builds a reusable library or a production module.

Directory structure:

```
module-name/
├── module_name/
│   ├── __init__.py
│   └── main.py
├── tests/
│   └── __init__.py
├── .env.example
├── .gitignore
├── README.md
├── requirements.txt
└── LICENSE
```

Required:
- `.gitignore` must contain `.env`
- `README.md` in English with the standard sections
(Description, Setup, Usage, Known, Limits)
- Logging setup in `__init__.py` as a pattern
- First commit: `feat: initial scaffold`

## 2. CLI tool

Command-line tool. When the project should be run via `python -m tool_name`
or a console command.

Directory structure:

```
tool-name/
├── tool_name/
│   ├── __init__.py
│   ├── __main__.py
│   └── cli.py
├── tests/
│   └── __init__.py
├── .gitignore
├── README.md
├── requirements.txt
└── LICENSE
```

Required:
- `__main__.py` as the entry point (`python -m tool_name`)
- `cli.py` parses arguments (`argparse`), one function per command
- `README.md` with a command-line usage example
- `.gitignore` with the Python standard

## 3. API service

Web service. When the project exposes endpoints and needs secrets
or a port.

Directory structure:

```
service-name/
├── service_name/
│   ├── __init__.py
│   ├── main.py
│   └── routes.py
├── tests/
│   └── __init__.py
├── .env.example
├── .gitignore
├── README.md
├── requirements.txt
└── LICENSE
```

Required:
- `.env` for secrets and the port, never in the repo
- `main.py` starts the server
- `routes.py` keeps the endpoints separate from the server configuration
- `README.md` with a list of the endpoints
- `.gitignore` must contain `.env`

## 4. Learning script (own exercises)

Minimal. When working through book examples or
writing small learning scripts of your own.

Directory structure:

```
project-name/
├── project_name.py
└── README.md
```

Required:
- Just a `.py` file plus an optional README
- No `requirements.txt` needed if it's stdlib only
- No `git init` needed if it stays purely local
- If you do use git: `.gitignore` with the Python standard

## Templates

### .gitignore (Python standard, all project types)

```
# Environment
.env
.env.local
.venv/
venv/

# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
dist/
*.egg-info/

# IDE
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db

# Testing
.pytest_cache/
.coverage
htmlcov/
```

### .env.example (module/service with API access)

```
# LLM API (if used)
ANTHROPIC_API_KEY=

# Local LLM (if used)
OLLAMA_HOST=http://localhost:11434

# Database (if relevant)
DATABASE_URL=

# Service (if relevant)
PORT=8000
```

### Logging setup (__init__.py)

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(logging.INFO)

handler = logging.StreamHandler()
formatter = logging.Formatter(
    "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
)
handler.setFormatter(formatter)
logger.addHandler(handler)
```

### requirements.txt (standard stack)

```
# LLM
anthropic>=0.40.0

# HTTP
requests>=2.31.0

# Environment
python-dotenv>=1.0.0
```

Pin exact versions if the module needs to run reproducibly.

## Gotchas

Common mistakes when scaffolding - avoid them.

### Committing `.env` into the repo

On the first commit after scaffolding: check that `.env`
is really in `.gitignore` *before* `git add .` runs.
Standard order: create `.gitignore` first, then the
other files.

### Folder names with spaces

Spaces in folder names break many tools (gh CLI, pip,
some Python modules), especially on Windows and with
cloud-sync paths. Always use a hyphen or underscore,
never a space.

### Module name vs. directory name

The Python module name (inner folder) needs an underscore
(`my_module`), the repo name can have a hyphen
(`my-module`). Keep both in mind when scaffolding —
directory with a hyphen, inner module with an underscore.

### Unpinned requirements.txt

A `requirements.txt` without version pinning (`anthropic`
instead of `anthropic>=0.40.0`) breaks later when the
library introduces a breaking change. At least `>=` with
the current version, better an exact `==` for production modules.

### A German-language README on a public repo

Public repos need an English README. If you naturally think
in German, write the README in English from the start when
scaffolding instead of translating it later.

### Wrong location

Code belongs in the project directory, not in a notes
or docs folder. Notes folders are for notes, not for
code — keep the two cleanly separate.

### Forgetting the LICENSE

Public modules need a LICENSE file (e.g. MIT). Without a
license nobody may legally use the code. Add it right away
when scaffolding, not "later".

### The first commit message

First commit after scaffolding: `feat: initial scaffold`
(Conventional Commits). Not "first commit" or "init".

## Example run

User: "I'm starting a new Python module, it should
become an HTTP client wrapper."

Claude Code:

1. Determines the project type → Python module (clear from the context)
2. Asks for the module name → User: "http-client-wrapper"
3. Sets up the directory structure:
   - Repo folder: `http-client-wrapper/` (with a hyphen)
   - Inner module: `http_client_wrapper/` (with an underscore)
4. Creates all required files from the templates
5. Writes `.gitignore` first, with `.env` in it
6. Creates the README in English with the standard sections
7. Adds the logging pattern in `__init__.py`
8. `git init`, then the first commit: `feat: initial scaffold`
9. Reports what was created, doesn't run ahead on its own

The user reviews it, corrects if needed, takes it over.

---
> Source: [andy-builds-ai/claude-code-skills](https://github.com/andy-builds-ai/claude-code-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
