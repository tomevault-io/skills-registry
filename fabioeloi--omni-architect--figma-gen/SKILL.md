---
name: figma-generator
description: Array de {node_id, type, name, preview_url, figma_url}. Use when this capability is needed.
metadata:
  author: fabioeloi
---

# Figma Generator

## Processo

1. Conectar à API Figma (REST API v1)
2. Criar/localizar página no arquivo
3. Para cada diagrama: criar Frame com auto-layout
4. Mapear nós Mermaid → componentes Figma
5. Aplicar design tokens (cores, tipografia, spacing)
6. Criar conexões visuais (arrows, lines)
7. Gerar variantes responsivas se aplicável
8. Adicionar anotações de dev
9. Criar página de índice com links

## Rate Limiting

- Exponential backoff: 1s, 2s, 4s, 8s (max 5 retries)
- Batch creation para minimizar chamadas API
- Cache de componentes já criados

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fabioeloi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
