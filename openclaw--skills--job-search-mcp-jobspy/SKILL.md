---
name: job-search-mcp-jobspy
description: This skill enables AI agents to search for job listings across multiple job boards Use when this capability is needed.
metadata:
  author: openclaw
---
# Job Search MCP Skill

This skill enables AI agents to search for job listings across multiple job boards
using the JobSpy MCP server.

JobSpy aggregates listings from sources such as LinkedIn, Indeed, Glassdoor,
ZipRecruiter, Google Jobs, and more into a unified interface.


## When to Use This Skill

Use this skill when you want to:
- Find job listings matching specific criteria (role, location, company)
- Search for remote or on-site positions
- Compare opportunities across job boards
- Retrieve salary information when available
- Find recently posted jobs
- Filter for “Easy Apply” roles

## Prerequisites
- Python 3.10+
- JobSpy MCP server installed and configured

## Installation & Setup

### macOS Setup (Homebrew Python)

Homebrew-managed Python is externally managed (PEP 668).
Create and activate a virtual environment before installing dependencies:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install mcp python-jobspy pandas pydantic

```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
