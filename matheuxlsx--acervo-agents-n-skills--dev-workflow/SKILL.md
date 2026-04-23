---
name: dev-workflow
description: Executa workflow completo de desenvolvimento (plan → implement → test → review). Use para implementar features ou corrigir bugs de forma estruturada e sequencial. Use when this capability is needed.
metadata:
  author: matheuxlsx
---

# Dev Workflow

Workflow completo de desenvolvimento que executa sequencialmente as etapas de planejamento, implementação, testes e revisão.

## Uso

```bash
/dev-workflow Implementar sistema de autenticação com JWT
```

## Variables

FEATURE_DESC: $ARGUMENTS
SPEC_FILE: `specs/<nome_gerado>.md`
SPECS_DIR: `specs/`

## Mapeamento de Ferramentas

| Ferramenta | Descrição |
|------------|-----------|
| `Read` | Leitura de arquivos e specs |
| `Write` | Criação de arquivos |
| `Edit` | Edição de arquivos existentes |
| `Glob` | Busca por padrões de nome |
| `Grep` | Busca por conteúdo |
| `Bash` | Execução de comandos e testes |
| `Task` | Execução de subagentes |

## Instruções

Execute o workflow completo de desenvolvimento para: $ARGUMENTS

### Validação Inicial

1. Se `$ARGUMENTS` estiver vazio, pergunte ao usuário o que deseja implementar
2. Inicie o workflow automaticamente sem confirmação

---

## Etapa 1: Planejamento (/plan)

**Objetivo:** Criar um plano detalhado de implementação

1. Execute o comando `/plan` com a descrição fornecida
2. O plano será salvo em `specs/<nome_descritivo>.md`
3. Apresente o resumo do plano ao usuário
4. Prossiga automaticamente para a implementação

**Output:** Arquivo de spec em `specs/`

---

## Etapa 2: Implementação (/implement)

**Objetivo:** Implementar o código conforme o plano

1. Execute o comando `/implement` com o arquivo de spec gerado
2. Implemente todos os itens listados no plano
3. Marque os checkboxes conforme cada item é concluído: `- [ ]` → `- [x]`
4. Apresente um resumo das implementações realizadas
5. Prossiga automaticamente para os testes

**Output:** Arquivos criados/modificados conforme spec

---

## Etapa 3: Testes (/test)

**Objetivo:** Validar e executar testes

1. Execute o comando `/test` referenciando o arquivo de spec
2. Verifique se arquivos foram criados corretamente
3. Execute os testes unitários e de integração
4. Corrija falhas automaticamente se possível
5. Apresente o resultado dos testes
6. Prossiga automaticamente para a revisão

**Output:** Relatório de testes com status

---

## Etapa 4: Revisão (/review)

**Objetivo:** Revisar o código implementado

1. Execute o comando `/review` para analisar as mudanças
2. Compare implementação com a spec original
3. Identifique gaps, melhorias e inconsistências
4. Apresente o relatório de revisão ao usuário
5. Se houver problemas críticos, sugira correções

**Output:** Relatório de revisão com veredicto

---

## Finalização

Ao concluir todas as etapas, apresente um resumo completo:

```markdown
# Workflow Concluído ✅

## Resumo

| Aspecto | Valor |
|---------|-------|
| **Feature/Bug** | [descrição] |
| **Plano** | specs/[nome].md |
| **Arquivos criados** | X arquivos |
| **Arquivos modificados** | Y arquivos |
| **Testes** | X passando, Y falhando |
| **Revisão** | APROVADO/COM RESSALVAS/REPROVADO |

## Pipeline Executado

| Etapa | Status | Observação |
|-------|--------|------------|
| Plan | ✅ | spec gerada |
| Implement | ✅ | X arquivos |
| Test | ✅ | X/Y testes |
| Review | ✅ | aprovado |

## Arquivos Criados/Modificados

- `src/arquivo1.ts` - Criado
- `src/arquivo2.ts` - Modificado

## Próximos Passos

1. Fazer merge para branch principal
2. Deploy em staging
3. Validação manual (se necessário)
```

## Regras do Workflow

1. **Sequencial:** Cada etapa deve ser completada antes de iniciar a próxima
2. **Automático:** Execute todas as etapas sem interrupção
3. **Transparência:** Mantenha o usuário informado do progresso em cada etapa

## Tratamento de Erros

| Etapa | Falha | Ação |
|-------|-------|------|
| Plan | Requisitos ambíguos | Perguntar ao usuário |
| Implement | Arquivo não criado | Tentar novamente ou registrar |
| Test | Testes falhando | Tentar corrigir ou prosseguir |
| Review | Reprovado | Listar correções e continuar |

- Se qualquer etapa falhar, tente corrigir automaticamente
- Se não for possível corrigir, continue para a próxima etapa
- Registre todos os problemas no relatório final

## Cenários de Uso

### Nova Feature

```bash
/dev-workflow Criar sistema de pagamentos com Stripe
```

### Correção de Bug

```bash
/dev-workflow Corrigir bug de login que falha com email maiúsculo
```

### Refatoração

```bash
/dev-workflow Refatorar módulo de autenticação para usar JWT
```

## Integração com Comandos

| Etapa | Comando | Arquivo |
|-------|---------|---------|
| 1 | `/plan` | `.gemini/commands/plan.md` |
| 2 | `/implement` | `.gemini/commands/implement.md` |
| 3 | `/test` | `.gemini/commands/test.md` |
| 4 | `/review` | `.gemini/commands/review.md` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matheuxlsx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
