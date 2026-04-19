---
name: python-project-setup
description: Sets up modern Python projects with uv tooling, src layout, and PEP8 standards. Handles both new and existing projects, presents interactive library selection for CLI/TUI apps, generates pyproject.toml, and provides complete scaffolding with type hints and proper structure.
metadata:
  author: tasanakorn
---

# Python Project Setup Skill

A specialized skill for setting up modern Python projects using uv tooling, best practices, and standardized project structures.

## Overview

This skill enables Claude to autonomously set up Python projects following modern best practices. It promotes the use of `uv` (the fast Python package manager), enforces PEP8 standards, type hints, and proper project structure with `src` layout.

## Capabilities

When activated, this skill provides:

1. **Modern Tooling with uv**
   - Use `uv` instead of pip/python directly
   - Initialize virtual environments: `uv venv`
   - Manage dependencies: `uv add`, `uv remove`
   - Sync dependencies: `uv sync`
   - Run scripts: `uv run`

2. **Project Structure Detection & Setup**
   - Detect if project is new or existing
   - Analyze current structure and adapt recommendations
   - Promote `src/package_name` layout
   - Create proper `__init__.py` files
   - Setup `__main__.py` entry points

3. **pyproject.toml Management**
   - Generate modern pyproject.toml
   - Configure project metadata
   - Setup dependencies and dev dependencies
   - Include tool configurations (ruff, mypy, pytest)
   - Define entry points for CLI applications

4. **Library Selection**
   - Present interactive menu for library choices
   - **CLI applications**: click (commands), rich (output), inquirer (interactive prompts)
   - **TUI applications**: textual (framework), rich (rendering)
   - **Pure libraries**: minimal dependencies
   - Smart recommendations based on project type

5. **Standards Enforcement**
   - PEP8 compliance via ruff
   - Type hints for all functions/methods
   - Proper docstring formats (Google/NumPy style)
   - Follow PEP8 and modern Python standards

## Usage

This skill activates automatically when:
- User requests: "Create a new Python project"
- User asks: "Setup Python project structure"
- User mentions: "Initialize Python package"
- User wants to: "Migrate to uv"
- Claude detects Python project initialization
- pyproject.toml creation or modification is discussed

**Automatic Defaults**:
- Uses current directory as project root
- Derives package name from directory name
- Detects and uses current Python version
- No tests (user can add later)
- Minimal dependencies by default

**Only ONE Question**:
- Project type + libraries combined (default: minimal)

## Project Setup Approach

### For New Projects

**IMPORTANT**: Always work in the current directory as the project root.

1. **Gather Requirements**
   - Package name (derive from current directory name by default)
   - **Ask ONE merged question**: Project type + libraries combined:
     ```
     What type of project? (press Enter for CLI with click+rich)
     [1] CLI with click+rich (default)
     [2] CLI with argparse
     [3] CLI with typer
     [4] TUI with textual
     [5] Generic Python Project
     ```
   - **Don't ask about tests** - skip by default

2. **Assume Current Directory & Python Version**
   - **Use current folder as project root** (don't create new directory)
   - Derive package name from current folder name (convert to valid Python identifier)
   - Example: `my-awesome-app/` → package name: `my_awesome_app`
   - **Detect current Python version** and use as project requirement
   - Example: `python --version` → `Python 3.11.5` → `requires-python = ">=3.11"`

3. **Check Environment**
   ```bash
   # Detect current Python version
   python --version  # Use this as the project's Python requirement

   # Verify uv is installed
   uv --version

   # If not installed, provide instructions:
   # macOS/Linux: curl -LsSf https://astral.sh/uv/install.sh | sh
   # Windows: powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
   ```

4. **Create Minimal Project Structure in Current Directory**
   ```
   ./                           # Current directory (project root)
   ├── src/
   │   └── package_name/
   │       ├── __init__.py
   │       └── __main__.py      # Entry point
   ├── pyproject.toml           # Minimal config
   ├── README.md
   └── .gitignore
   ```

   **Note**: No tests/, no .python-version - keep it minimal!

5. **Skip Tests by Default**
   - Do NOT ask about tests
   - Do NOT create tests/ directory
   - Do NOT include pytest in dev dependencies
   - User can add tests later if needed

6. **Generate Minimal pyproject.toml**
   ```toml
   [project]
   name = "package-name"
   version = "0.1.0"
   description = "Project description"
   authors = [
       {name = "Author Name", email = "author@example.com"}
   ]
   readme = "README.md"
   requires-python = ">=3.11"  # Use detected Python version (e.g., 3.11, 3.12, etc.)
   dependencies = [
       # Based on library selection
   ]

   [project.optional-dependencies]
   dev = [
       "ruff>=0.1.0",
       "mypy>=1.8.0",
   ]
   # Note: No pytest by default - add when needed

   [project.scripts]
   package-name = "package_name.__main__:main"

   [build-system]
   requires = ["hatchling"]
   build-backend = "hatchling.build"

   [tool.ruff]
   line-length = 88
   target-version = "py311"
   select = ["E", "F", "I", "N", "W", "UP"]

   [tool.mypy]
   python_version = "3.11"
   strict = true
   warn_return_any = true
   warn_unused_configs = true

   [tool.pytest.ini_options]
   testpaths = ["tests"]
   python_files = ["test_*.py"]
   ```

8. **Initialize with uv in Current Directory**
   ```bash
   # Already in project directory (current folder)

   # Create virtual environment
   uv venv

   # Sync dependencies from pyproject.toml
   uv sync

   # Add project in editable mode
   uv pip install -e .

   # Add development dependencies (includes pytest if tests opted in)
   uv sync --group dev
   ```

9. **Create Entry Point Examples**

   For `src/package_name/__main__.py`:
   ```python
   """Main entry point for package_name."""

   def main() -> None:
       """Execute the main program."""
       print("Hello from package_name!")

   if __name__ == "__main__":
       main()
   ```

   For CLI apps with click:
   ```python
   """Main entry point for package_name CLI."""

   import click
   from rich.console import Console

   console = Console()

   @click.group()
   @click.version_option()
   def main() -> None:
       """Package name CLI application."""
       pass

   @main.command()
   @click.option("--name", default="World", help="Name to greet")
   def hello(name: str) -> None:
       """Greet someone."""
       console.print(f"[bold green]Hello, {name}![/bold green]")

   if __name__ == "__main__":
       main()
   ```

### For Existing Projects

**IMPORTANT**: Always work in the current directory, don't create subdirectories.

1. **Analyze Current Directory Structure**
   - Check for setup.py, requirements.txt, or pyproject.toml
   - Identify current package structure
   - Assess if src layout is used
   - **Detect existing tests/** directory

2. **Detect Context**
   - Is uv already being used?
   - What's the current dependency management approach?
   - Are there existing entry points?
   - **Are tests already present?** (skip test skeleton question if yes)

3. **Skip Tests Question**
   - Do NOT ask about tests for existing projects
   - User can add tests manually if needed

4. **Provide Migration Path**
   ```bash
   # Already in project directory (current folder)

   # Convert requirements.txt to pyproject.toml
   # Create pyproject.toml with [project.dependencies]

   # Initialize uv for existing project
   uv venv

   # Import existing requirements
   uv pip install -r requirements.txt

   # Generate lock file
   uv sync
   ```

5. **Recommend Structure Improvements**
   - Suggest moving to src layout if not present
   - Identify missing __init__.py files
   - Recommend adding __main__.py if needed
   - Suggest adding type hints if missing

6. **Adapt, Don't Force**
   - Respect existing conventions if reasonable
   - Provide gradual migration suggestions
   - Explain benefits of each recommendation

## Output Format

Provide setup results in this format:

```markdown
## Python Project Setup Complete

### Project Directory
**Working in**: `./` (current directory)
**Package name**: `package_name`

### Project Structure
[Show created directory tree relative to current directory]

### Configuration
- **Python version**: [Detected from current environment, e.g., 3.11+]
- **Package manager**: uv
- **Layout**: src layout
- **Entry point**: src/package_name/__main__.py
- **Tests**: Not included (add later if needed)

### Dependencies Installed
**Main dependencies:**
- [list dependencies]

**Development dependencies:**
- ruff (linting)
- mypy (type checking)

### Next Steps
1. Activate virtual environment:
   \`\`\`bash
   source .venv/bin/activate  # Linux/macOS
   .venv\\Scripts\\activate     # Windows
   \`\`\`

2. Run your application:
   \`\`\`bash
   uv run python -m package_name
   # or
   uv run package-name
   \`\`\`

3. Add new dependencies:
   \`\`\`bash
   uv add package-name
   \`\`\`

4. Lint code:
   \`\`\`bash
   uv run ruff check .
   uv run mypy src
   \`\`\`

### Development Commands
- Add dependency: `uv add package-name`
- Add dev dependency: `uv add --dev package-name`
- Remove dependency: `uv remove package-name`
- Update dependencies: `uv sync`
- Run script: `uv run python script.py`
```

## Standards Enforced

### Always Use uv Commands
- ❌ `pip install package`
- ✅ `uv add package`

- ❌ `pip install -r requirements.txt`
- ✅ `uv sync`

- ❌ `python script.py`
- ✅ `uv run python script.py`

- ❌ `python -m venv .venv`
- ✅ `uv venv`

### Project Structure Standards
```
src/package_name/          # Use src layout
├── __init__.py           # Package marker (can be empty or contain version)
├── __main__.py           # Entry point with main() function
├── module1.py            # Application modules
└── module2.py
```

### Code Standards
- All functions must have type hints
- Use `"""Docstrings"""` for all public functions/classes
- Follow PEP8 (enforced by ruff)
- Line length: 88 characters (Black standard)
- Use absolute imports from package root

### Example Type-Hinted Code
```python
from typing import Optional

def process_data(
    input_data: list[str],
    max_items: int = 10,
    filter_empty: bool = True
) -> dict[str, int]:
    """Process input data and return statistics.

    Args:
        input_data: List of strings to process
        max_items: Maximum number of items to process
        filter_empty: Whether to filter out empty strings

    Returns:
        Dictionary with processing statistics
    """
    result: dict[str, int] = {}
    # Implementation
    return result
```

## Library Recommendations

### For CLI Applications
```toml
dependencies = [
    "click>=8.1.0",      # Command-line interface framework
    "rich>=13.7.0",      # Beautiful terminal output
    "inquirer>=3.2.0",   # Interactive prompts
]
```

### For TUI Applications
```toml
dependencies = [
    "textual>=0.47.0",   # Modern TUI framework
    "rich>=13.7.0",      # Rendering engine
]
```

### For Libraries
```toml
dependencies = [
    # Only essential dependencies
    # Keep it minimal
]
```

## Best Practices

1. **Work in current directory** - Don't create nested project folders
2. **Use current Python version** - Detect from environment, don't ask
3. **Ask one merged question** - Project type + libraries combined
4. **Start with minimal dependencies** - Add more as needed
5. **Always include type hints** - Helps with IDE support and catches bugs
6. **Use src layout** - Prevents import issues and packaging problems
7. **Pin major versions only** - e.g., `>=8.1.0` not `==8.1.0`
8. **Separate dev dependencies** - Use `[project.optional-dependencies]`
9. **Document with docstrings** - Every public function and class
10. **Don't ask about tests** - Skip by default, users add when ready
11. **Use ruff for linting** - Fast, comprehensive, replaces flake8/isort/black

## Common Patterns

### CLI Application with Click + Rich
```python
import click
from rich.console import Console
from rich.table import Table

console = Console()

@click.command()
@click.argument("name")
@click.option("--count", default=1, help="Number of greetings")
def greet(name: str, count: int) -> None:
    """Greet NAME COUNT times."""
    for _ in range(count):
        console.print(f"[bold blue]Hello, {name}![/bold blue]")
```

### TUI Application with Textual
```python
from textual.app import App, ComposeResult
from textual.widgets import Header, Footer, Button

class MyApp(App):
    """A simple Textual application."""

    def compose(self) -> ComposeResult:
        yield Header()
        yield Button("Click me!", id="btn")
        yield Footer()

    def on_button_pressed(self, event: Button.Pressed) -> None:
        """Handle button press."""
        self.notify("Button clicked!")

if __name__ == "__main__":
    app = MyApp()
    app.run()
```

## Integration

This skill works seamlessly with:
- New Python project creation
- Existing project modernization
- Migration from pip to uv
- Package structure refactoring
- Dependency management tasks

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tasanakorn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
