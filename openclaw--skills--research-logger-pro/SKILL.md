---
name: research-logger
description: > Use when this capability is needed.
metadata:
  author: openclaw
---

# Research Logger 📝🔬

Search + auto-save pipeline. Every research query is logged to SQLite with Langfuse tracing.

## When to Use

- Research that you want to save and recall later
- Building a knowledge base from repeated searches
- Reviewing past research on a topic
- Creating an audit trail of research decisions

## Usage

```bash
# Search and auto-log
python3 {baseDir}/scripts/research_logger.py log quick "what is RAG"
python3 {baseDir}/scripts/research_logger.py log pro "compare vector databases" --topic "databases"

# Search past research
python3 {baseDir}/scripts/research_logger.py search "vector databases"

# View recent entries
python3 {baseDir}/scripts/research_logger.py recent --limit 5
```

## Credits

Built by [M. Abidi](https://www.linkedin.com/in/mohammad-ali-abidi) | [agxntsix.ai](https://www.agxntsix.ai)
[YouTube](https://youtube.com/@aiwithabidi) | [GitHub](https://github.com/aiwithabidi)
Part of the **AgxntSix Skill Suite** for OpenClaw agents.

📅 **Need help setting up OpenClaw for your business?** [Book a free consultation](https://cal.com/agxntsix/abidi-openclaw)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
