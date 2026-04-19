---
name: tester-analysis
description: Validação e teste de aplicações web. Use para: testar fluxos de UI, validar responsividade, verificar erros de console/rede, testar acessibilidade, gerar relatórios de validação. Triggers: 'validar', 'testar', 'verificar', 'QA', 'browser test', 'responsividade'. Use when this capability is needed.
metadata:
  author: guilherme-xmatch
---

# Tester Analysis Skill

Skill para validação e teste de aplicações web em runtime.

## Quando Usar
- Validar implementação de telas/features.
- Testar fluxos de usuário (cenário feliz e de erro).
- Verificar responsividade em múltiplos viewports.
- Identificar erros de console, rede ou performance.
- Gerar relatórios estruturados de validação.

## Processo de Teste

### 1. Preparação
- Identificar URLs/rotas a testar.
- Definir fluxo principal (cenário feliz).
- Definir pelo menos um cenário de erro.
- Escolher viewports: mobile (375x667) e desktop (1440x900) como mínimo.

### 2. Execução
- Acessar URL base e navegar clicando nos itens de acesso.
- Para cada fluxo:
  - Verificar carregamento da página (sem erros).
  - Interagir com elementos (click, type, submit).
  - Verificar feedback visual e funcional.
  - Capturar evidências (screenshots, logs).

### 3. Validações Obrigatórias
- Fluxo principal completo (cenário feliz).
- Pelo menos 1 cenário de erro (form inválido, dados ausentes).
- Responsividade em 2+ viewports.
- Console sem erros JavaScript críticos.
- Requisições de rede sem falhas inesperadas.

### 4. Classificação de Problemas
| Gravidade | Definição | Exemplo |
|-----------|-----------|---------|
| **Crítica** | Bloqueia uso da feature | Botão submit não funciona |
| **Média** | Impacta UX significativamente | Layout quebrado em mobile |
| **Baixa** | Cosmético ou menor | Espaçamento inconsistente |

## Template de Relatório

```markdown
## Relatório de Validação — [Nome da Feature]

### Ambiente
- URL: [URL testada]
- Browser: Chrome [versão]
- Data: [data]

### Viewports Testados
- Mobile: 375x667
- Desktop: 1440x900

### Cenários Testados
| # | Cenário | Tipo | Resultado |
|---|---------|------|-----------|
| 1 | [Descrição] | Feliz | ✅/❌ |
| 2 | [Descrição] | Erro | ✅/❌ |

### Problemas Encontrados
| # | Descrição | Gravidade | Viewport | Sugestão |
|---|-----------|-----------|----------|----------|
| 1 | [Problema] | Crítica/Média/Baixa | Mobile/Desktop/Ambos | [Como corrigir] |

### Console/Rede
- Erros de console: [listar ou "nenhum"]
- Requisições com falha: [listar ou "nenhuma"]

### Veredicto
- APROVADO / REPROVADO / APROVADO COM RESSALVAS
```

## Boas Práticas
- Sempre acessar o link base primeiro e navegar clicando.
- Capturar evidências visuais sempre que possível.
- Testar com dados realistas (não apenas "test", "abc").
- Verificar acessibilidade básica (contraste, foco, labels).
- Relatar problemas com contexto suficiente para reprodução.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guilherme-xmatch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
