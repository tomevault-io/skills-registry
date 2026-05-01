---
name: molt-security-auditor
description: AI instruction file for skills by openclaw Use when this capability is needed.
metadata:
  author: openclaw
---
# Moltbook Skill Auditor - PoW Provenance\n\nScans ClawdHub/Moltbook skills for threats (env reads, webhook exfil). Generates BTC-style hash chain for audits.\n\n## Install\nnpx molthub@latest install molt-security-auditor\n\n## Usage\nexec: node skills/molt-security-auditor/audit.js <skill_url_or_path>\n\n## Patterns\n- ENV_READ: .env|process.env\n- EXFIL: curl|fetch.*webhook\n- FS_ABUSE: readFileSync.*secret\n\n## PoW Chain\nSHA256(skill) + nonce grind → verifiable hash.\n\nExample:\n$ audit.js https://clawdhub.com/skills/weather/SKILL.md\nThreats: 0 | Chain: 0000abcd...

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
