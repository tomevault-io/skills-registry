---
name: exa-synergy
description: Neural web research with Exa AI SDK. Auto-selects strategy: fast search for quick answers, deep research for comprehensive analysis, harvest for collecting lists/databases. Use when user needs web search, research, data gathering, or information discovery. Use when this capability is needed.
metadata:
  author: rikitikitavi2012-debug
---

# Exa Synergy — Neural Web Research

Intelligent web research tool using Exa AI SDK (v2.3+) with automatic strategy selection.

## Installation

```bash
# Install dependencies
pip install -r requirements.txt

# Or use setup script
bash /a0/usr/skills/setup.sh

# Set API key (get at https://exa.ai)
export EXA_API_KEY="your_api_key_here"
# Or add to /a0/.env: EXA_API_KEY=your_api_key_here
```

## When to Use

Use this skill when you need to:
- Search the web for information
- Research a topic in depth
- Collect lists of companies, emails, or data
- Find documentation or tutorials
- Gather competitive intelligence

## Strategy Auto-Selection

The tool automatically selects the best strategy based on query:

| Strategy | Use Case | Example Queries |
|----------|----------|-----------------|
| **fast** | Quick answers | "What is...", "How do I..." |
| **deep** | Comprehensive research | "Explain in detail...", "Research...", "Analyze..." |
| **harvest** | Data collection | "List all...", "Find companies...", "Collect emails..." |

## Usage

### Via Python Script
```bash
python /a0/usr/skills/exa-synergy/scripts/exa_synergy.py --goal "your research goal" --mode auto --num_results 5
```

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `goal` | str | required | What you want to research |
| `mode` | str | auto | Strategy: auto/fast/deep/harvest |
| `num_results` | int | 5 | Number of results (1-20) |
| `save_to` | str | optional | File path to save results |

### Examples

1. **Quick search:**
   ```bash
   python /a0/usr/skills/exa-synergy/scripts/exa_synergy.py --goal "Python async best practices" --mode fast
   ```

2. **Deep research:**
   ```bash
   python /a0/usr/skills/exa-synergy/scripts/exa_synergy.py --goal "Compare React vs Vue performance" --mode deep --num_results 10
   ```

3. **Data harvesting:**
   ```bash
   python /a0/usr/skills/exa-synergy/scripts/exa_synergy.py --goal "AI startups in Europe 2024" --mode harvest --save_to /a0/tmp/research.md
   ```

## Requirements

- `EXA_API_KEY` must be set in environment or `/a0/.env` file
- `exa-py>=2.3.0` (installed via requirements.txt)

## Output Format

Returns structured results with:
- Title and URL for each result
- Content summary
- Relevance score
- Publication date (when available)

## Files

```
/a0/usr/skills/exa-synergy/
├── scripts/
│   └── exa_synergy.py
├── requirements.txt
└── SKILL.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rikitikitavi2012-debug) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
