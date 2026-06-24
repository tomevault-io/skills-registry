---
name: graphql-queries
description: Constructing GraphQL query client configurations and mutation payloads in Python. Use when this capability is needed.
metadata:
  author: Lord1Egypt
---

# Graphql Queries

## Overview
GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data.

## When to Use This Skill
Use to interface with APIs that expose GraphQL endpoints (e.g., GitHub GraphQL API v4).

## Quick Start (with runnable code examples)

```python
import requests

url = 'https://api.github.com/graphql'
headers = {'Authorization': 'token ghp_...'}

query = """
query {
  viewer {
    login
    bio
  }
}
"""

r = requests.post(url, json={'query': query}, headers=headers)
print(r.json())
```

## Advanced Usage
Handle GraphQL input variables, define fragment structures, configure mutations, and parse paginated cursor queries.

## Key References
- [GraphQL official documentation](https://graphql.org/)

## Dependencies
- requests>=2.28.0

---
> Source: [Lord1Egypt/ai-skillforge](https://github.com/Lord1Egypt/ai-skillforge) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
