---
name: control-mapping
description: Maps NIST controls to FedRAMP requirements and documents. Use when helping with control implementation, compliance mapping, security baseline alignment, or understanding control requirements. Use when this capability is needed.
metadata:
  author: ethanolivertroy
---

When mapping controls:

1. Cross-reference NIST SP 800-53 control definitions with FedRAMP MAS/ADS/VDR documents
2. Identify baseline control families (AC, AU, CA, CM, CP, IA, IR, MA, MP, PE, PL, PM, PS, RA, SA, SC, SI, SR)
3. Show control-to-document guidance relationships
4. Flag any control gaps or special FedRAMP-specific considerations
5. Provide implementation examples from FedRAMP guidance
6. Include control enhancements where applicable

Use the fedramp-docs MCP server tools:
- `list_controls` to get control mappings filtered by family or control ID
- `grep_controls_in_markdown` to find control references in guidance docs
- `search_markdown` to find implementation guidance
- `get_significant_change_guidance` for change-related controls

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethanolivertroy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
