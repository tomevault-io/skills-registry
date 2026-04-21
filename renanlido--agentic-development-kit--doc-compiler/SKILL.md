---
name: doc-compiler
description: Especialista em ler, indexar e compilar documentações locais para fornecer contexto preciso à IA. Use quando precisar entender como o sistema funciona baseando-se em arquivos .md, .txt ou .pdf na pasta de docs. Use when this capability is needed.
metadata:
  author: renanlido
---

# Document Compiler Skill

Esta skill ajuda a compilar contexto de documentações espalhadas pelo projeto.

## Workflow

1.  **Localizar**: Quando o usuário pedir informações sobre um tópico (ex: "Como funciona a autenticação?"), procure arquivos relevantes nas pastas `docs/`, `.claude/docs/` ou `references/` usando `glob`.
2.  **Ler**: Leia os arquivos encontrados.
3.  **Sintetizar**: Não apenas despeje o texto. Resuma as partes relevantes para a pergunta atual.

## Referências

Se houver documentações padrão que devem ser sempre consultadas, coloque-as na pasta `references/` desta skill.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/renanlido) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
