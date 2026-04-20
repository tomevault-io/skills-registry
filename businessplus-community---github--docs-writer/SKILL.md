---
name: docs-writer
description: Python documentation guidelines. Use when writing docstrings, README content, API docs, or changelog entries. Target audience is CDD administrators. Use when this capability is needed.
metadata:
  author: businessplus-community
---

# Documentation Writer - Python

Guidelines for writing clear Python documentation for CDD administrators.

## Target Audience

**CDD administrators** - NOT Python developers. Prioritize:
- Clarity over technical depth
- Practical examples over theory
- Common use cases over edge cases

## When to Use

- Adding new public functions/classes
- Updating README
- Recording changes in CHANGELOG
- Writing module documentation

## Documentation Types

### 1. Google-style Docstrings

**For public functions and methods:**

```python
def generate_report(data: dict, format: str = "pdf") -> bytes:
    """Generate a CDD report from data.

    Creates a formatted report suitable for administrator review.
    Supports PDF and CSV output formats.

    Args:
        data: Report data as a dictionary with required keys:
            - 'title': Report title
            - 'records': List of record dictionaries
        format: Output format, either "pdf" or "csv". Defaults to "pdf".

    Returns:
        Report content as bytes, ready for file writing.

    Raises:
        ValueError: If format is not "pdf" or "csv".
        KeyError: If required keys are missing from data.

    Example:
        >>> data = {"title": "Monthly Report", "records": [...]}
        >>> content = generate_report(data, format="pdf")
        >>> with open("report.pdf", "wb") as f:
        ...     f.write(content)
    """
```

**For simple functions (one-liner):**

```python
def get_version() -> str:
    """Return the current library version."""
    return __version__
```

### 2. Module Docstrings

Place at the top of each module file:

```python
"""CDD report generation utilities.

This module provides helper functions for generating formatted reports
from CDD data sources. Designed for CDD administrators creating custom
reports.

Example:
    Basic usage::

        from bpc_pycdd import generate_report

        data = load_cdd_data()
        report = generate_report(data)

Attributes:
    DEFAULT_FORMAT: The default output format ("pdf").
    SUPPORTED_FORMATS: List of supported output formats.
"""
```

### 3. Class Docstrings

```python
class ReportBuilder:
    """Builder for constructing CDD reports step by step.

    Use when you need fine-grained control over report generation,
    such as adding sections incrementally or customizing headers.

    Attributes:
        title: The report title.
        sections: List of report sections added so far.

    Example:
        >>> builder = ReportBuilder("Monthly Summary")
        >>> builder.add_section("Overview", overview_data)
        >>> builder.add_section("Details", detail_data)
        >>> report = builder.build()
    """
```

### 4. README Structure

```markdown
# bpc-pycdd

Python library for generating CDD reports.

## Installation

```bash
pip install bpc-pycdd
```

## Quick Start

```python
from bpc_pycdd import generate_report

# Load your CDD data
data = {...}

# Generate report
report = generate_report(data)
```

## Features

- Feature 1: Brief description
- Feature 2: Brief description

## Documentation

Full API documentation: [link]

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md)

## License

MIT License - see [LICENSE](LICENSE)
```

### 5. CHANGELOG Format

Follow [Keep a Changelog](https://keepachangelog.com/en/1.1.0/):

```markdown
# Changelog

All notable changes to this project will be documented in this file.

## [Unreleased]

### Added
- New feature description

### Changed
- What was modified

### Fixed
- Bug that was fixed

## [0.2.0] - 2026-02-15

### Added
- `generate_report()` function for PDF output
- Support for CSV format

### Changed
- Improved error messages for missing data

## [0.1.0] - 2026-02-01

### Added
- Initial project structure
- Basic library skeleton
```

**Categories (use only what applies):**
- **Added** - New features
- **Changed** - Changes to existing features
- **Deprecated** - Soon-to-be removed features
- **Removed** - Removed features
- **Fixed** - Bug fixes
- **Security** - Security fixes

## Writing Guidelines

### Do

- **Start with a verb** for function docstrings: "Generate...", "Return...", "Calculate..."
- **Include examples** for all public APIs
- **Keep Args descriptions concise** - one line if possible
- **Use backticks** for code references: `function_name`, `ClassName`
- **Document exceptions** that callers should handle

### Don't

- **Don't explain the obvious**: `"""Return the name."""` not `"""This function returns the name of the object by accessing the _name attribute."""`
- **Don't document implementation details**: Users care about *what*, not *how*
- **Don't use jargon**: Write for CDD administrators, not Python experts
- **Don't over-document**: Simple getters/setters don't need multi-line docstrings

## Anti-patterns to Avoid

| Anti-pattern | Better |
|--------------|--------|
| `"""Gets the thing."""` | `"""Return the thing."""` |
| No example in public API | Always include Example section |
| Implementation in docstring | Document behavior, not code |
| Missing Args/Returns | Always document parameters and return values |
| Outdated docstring | Update docs when code changes |

## Docstring Checklist

Before completing documentation:

- [ ] All public functions have docstrings
- [ ] Complex functions have Args, Returns, Raises, Example
- [ ] Simple functions have one-line docstrings
- [ ] Module has a module-level docstring
- [ ] Classes have class-level docstrings
- [ ] Examples are runnable (tested with doctest if applicable)
- [ ] Language is clear for non-Python developers

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/businessplus-community) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
