---
name: catalog
description: Catálogo simples do estúdio (hello world) Use when this capability is needed.
metadata:
  author: openclaw
---

Quando o usuário perguntar por serviços/preços, execute um comando local que retorna JSON e então responda de forma curta e clara.

Use a ferramenta de execução de comandos (Exec Tool) para rodar:

node {baseDir}/catalog.js

O comando retorna uma lista JSON de serviços com `name`, `price`, `duration`.
Não invente valores: use apenas o JSON retornado.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
