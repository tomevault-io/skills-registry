---
name: consultant-workflow
description: Workflow de consultoria iterativa. Transforma ideias vagas em especificações técnicas usando Framework das 3 Perguntas, loop de perguntas categorizadas (máx 5 rodadas), e documentação hierárquica. Ver `~/.config/opencode/workflows/consultor-consolidated.md` para detalhes completos. Use when this capability is needed.
metadata:
  author: matheuxlsx
---

# Consultant Workflow

Transforma ideias vagas em especificações técnicas através de consultoria iterativa com Framework das 3 Perguntas e hierarquia de specs.

## Quando usar

- Ideia vaga de projeto
- Precisar amadurecer requisitos
- Quiser gerar specs hierárquicas
- Precisar de consultoria para definir arquitetura

## Framework das 3 Perguntas (Risco Zero)

1. **Produto:** O que é?
2. **Público:** Quem usa e qual dor resolve?
3. **Sucesso:** O que define que deu certo?

## Comandos

| Comando | Função |
|---------|--------|
| `/consult` | Perguntas categorizadas iterativas |
| `/expand` | Aprofunda tópico específico |
| `/suggest` | Sugestões de arquitetura |
| `/finalize` | Gera documentos técnicos |

## Templates

Todos os templates estão em `~/.config/opencode/templates/consultor/`:
- `01-discovery.md` - Framework das 3 Perguntas
- `02-analysis.md` - Perguntas iniciais
- `03-expansion.md` - Perguntas de expansão
- `04-checkpoint.md` - Checkpoint de satisfação
- `05-identity.md` - Identidade visual
- `06-synthesis.md` - Síntese e sugestões

## Documentação Detalhada

Para documentação completa do workflow, consulte:
`~/.config/opencode/workflows/consultor-consolidated.md`

## Regras

- MÁXIMO 5 rodadas de perguntas
- Roadmap com MÍNIMO 3 fases
- Brainstorming de identidade é obrigatório
- Hierarquia de specs: `fase-X-Y-Z-nome.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matheuxlsx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
