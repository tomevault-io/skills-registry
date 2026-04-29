---
name: languages
description: Master programming languages for backend development. Learn language selection, fundamentals, and ecosystem for JavaScript, Python, Go, Java, C#, PHP, Ruby, and Rust. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Programming Languages Skill

**Bonded to:** `programming-fundamentals-agent`

---

## Quick Start

```bash
# Invoke languages skill
"Help me choose a programming language for my backend project"
"Set up Python development environment"
"Compare Go vs Rust for microservices"
```

---

## Instructions

1. **Assess Requirements**: Evaluate project needs (performance, scalability, team expertise)
2. **Compare Languages**: Use decision matrix below
3. **Select Language**: Choose based on requirements and constraints
4. **Set Up Environment**: Configure IDE, package manager, linters
5. **Master Ecosystem**: Learn frameworks, testing tools, deployment

---

## Language Comparison Matrix

| Language | Best For | Performance | Learning | Ecosystem |
|----------|----------|-------------|----------|-----------|
| JavaScript/Node.js | Real-time, Full-stack | Good | Easy | Excellent |
| Python | ML/AI, APIs, Scripting | Moderate | Easy | Excellent |
| Go | Microservices, Cloud | Excellent | Medium | Good |
| Java | Enterprise, Android | Good | Medium | Excellent |
| C# | Microsoft, Games | Good | Medium | Good |
| Rust | Systems, Performance | Excellent | Hard | Growing |
| PHP | Web, WordPress | Moderate | Easy | Good |
| Ruby | Web, Startups | Moderate | Easy | Good |

---

## Decision Tree

```
Project Requirements?
    │
    ├─→ ML/AI/Data Science → Python
    │
    ├─→ High Performance Required?
    │     ├─→ Systems level → Rust
    │     └─→ Cloud/Microservices → Go
    │
    ├─→ Enterprise/Large Team → Java or C#
    │
    ├─→ Rapid Prototyping → Python or Ruby
    │
    └─→ Full-stack/Real-time → JavaScript/Node.js
```

---

## Examples

### Example 1: Language Selection
```
Input:  "I need to build a high-performance microservices API"
Output: Recommend Go or Rust
        - Go: Simpler, faster development, excellent concurrency
        - Rust: Maximum performance, memory safety, steeper learning curve
```

### Example 2: Environment Setup
```
Input:  "Set up Python development environment"
Output:
1. Install Python 3.12+ via pyenv
2. Create virtual environment: python -m venv .venv
3. Install dependencies: pip install -r requirements.txt
4. Configure IDE (VS Code + Python extension)
5. Set up linting: ruff, mypy
```

### Example 3: Package Management
```
Input:  "Configure dependency management for Node.js"
Output:
1. Initialize: npm init -y
2. Add dependencies: npm install express
3. Lock versions: package-lock.json
4. Configure .npmrc for registry
```

---

## Troubleshooting

### Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| `command not found` | Not in PATH | Add to shell profile |
| Version mismatch | Wrong version active | Use version manager (nvm, pyenv) |
| Package conflicts | Incompatible versions | Use lockfile, check compatibility |
| Build errors | Missing system deps | Install required libraries |

### Debug Commands

```bash
# Check versions
node -v && npm -v
python --version && pip --version
go version
rustc --version && cargo --version

# Check PATH
echo $PATH | tr ':' '\n' | grep -E 'node|python|go|rust'
```

---

## Test Template

```python
# tests/test_language_selection.py
import pytest

class TestLanguageSelection:
    def test_recommends_go_for_microservices(self):
        requirements = {"type": "microservices", "performance": "high"}
        result = select_language(requirements)
        assert result["language"] in ["go", "rust"]
        assert result["confidence"] > 0.8

    def test_recommends_python_for_ml(self):
        requirements = {"type": "ml", "team_expertise": ["python"]}
        result = select_language(requirements)
        assert result["language"] == "python"
```

---

## References

See `references/` directory for:
- `LANGUAGE_GUIDE.md` - Detailed language comparison
- `language-comparison.yaml` - Structured data

---

## Resources

- [Node.js Documentation](https://nodejs.org/docs/)
- [Python Documentation](https://docs.python.org/3/)
- [Go Documentation](https://golang.org/doc/)
- [Rust Book](https://doc.rust-lang.org/book/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
