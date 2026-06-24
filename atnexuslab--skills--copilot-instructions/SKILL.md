---
name: copilot-instructions
description: Use quando precisar criar ou atualizar instruções do Copilot CLI — copilot-instructions.md, SKILL.md, .agent.md ou arquivos de instrução por caminho. Use when this capability is needed.
metadata:
  author: ATNexusLab
---

# Copilot Instructions

> ⚠️ EXPERIMENTAL — Ainda não validado em produção.

## Quando Usar

- Criar ou atualizar o arquivo `.github/copilot-instructions.md` do repositório
- Criar ou atualizar uma `SKILL.md` de conhecimento reutilizável
- Criar ou atualizar um `.agent.md` de persona especializada
- Configurar instruções por caminho de arquivo (`.instructions.md`)

## Tipos de Instrução Copilot CLI

| Tipo | Arquivo | Escopo |
|------|---------|--------|
| Global | `~/.copilot/instructions.md` | Todas as sessões do usuário |
| Repositório | `.github/copilot-instructions.md` | Todo o repositório |
| Por caminho | `.github/instructions/*.instructions.md` com `applyTo` no frontmatter | Arquivos específicos |
| Skill | `skills/<name>/SKILL.md` | Carregada sob demanda por agente |
| Agent | `agents/<name>.agent.md` | Contexto isolado do subagente |

## Formato: copilot-instructions.md

Arquivo de instruções globais do repositório. Deve definir:

```markdown
# [Nome do Projeto] — Copilot Instructions

## Contexto
[O que é o projeto, stack principal, idioma de resposta]

## Convenções
[Convenções de código, nomenclatura, padrões obrigatórios]

## Protocolo de Resposta
[Como o agente deve trabalhar — etapas, aprovação, registro]

## Roster de Agentes
[Tabela de agentes disponíveis e quando chamar cada um]

## Nunca Faça
[Anti-padrões explícitos para este repositório]
```

## Formato: SKILL.md

Conhecimento procedural reutilizável. Frontmatter mínimo obrigatório:

```yaml
---
name: skill-name
description: Quando usar esta skill em uma frase precisa.
license: MIT
---
```

Campos opcionais: `type: skill`, `targets: [copilot-cli]`.

Corpo do SKILL.md deve conter:
- **Quando Usar** — gatilhos precisos
- **Checklist ou Workflow** — passo-a-passo executável
- **Padrões** — código de referência, exemplos
- **Nunca Faça** — anti-padrões explícitos

## Formato: .agent.md

Persona isolada com contexto próprio. Frontmatter obrigatório:

```yaml
---
name: agent-name
description: [Persona]. Use quando [gatilho preciso].
tools: ["read", "search", "edit"]
---
```

Regras críticas para `.agent.md`:
- **Deve ser COMPLETO** — persona + workflow + nunca-faz + protocolo de escalamento
- **Sem ponteiros** — não escrever "use a skill X para detalhes"; incorporar o essencial inline
- **Ferramentas mínimas** — listar só as tools que o agente realmente precisa
- **Persona forte** — o agente deve ter uma voz e perspectiva clara

Corpo do `.agent.md` deve conter:
- Identidade e papel
- Perguntas de discovery (se aplicável)
- Workflow passo-a-passo
- Protocolo de escalamento
- O que nunca fazer

## Formato: Path-Specific Instructions

```yaml
---
applyTo: "**/*.test.ts"
---

# Instruções para arquivos de teste TypeScript

[Convenções específicas para este tipo de arquivo]
```

## Quando Usar Cada Tipo

| Cenário | Tipo de instrução |
|---------|------------------|
| Convenções globais do projeto | `copilot-instructions.md` |
| Regras para um tipo de arquivo específico | Path-specific `.instructions.md` |
| Conhecimento de domínio reutilizável | `SKILL.md` |
| Persona com workflow completo | `.agent.md` |

## Checklist de Qualidade

Para qualquer instrução criada:
- [ ] Frontmatter com `name` e `description` preenchidos
- [ ] Conteúdo em pt-BR (padrão deste repositório)
- [ ] Sem TODO ou placeholder vazio no conteúdo
- [ ] Instruções executáveis, não só conceituais
- [ ] Sem repetição de conteúdo já em outra instrução
- [ ] Tag `⚠️ EXPERIMENTAL` se ainda não validado em produção

## Passos

### 1. Identificar o escopo da instrução

Determinar onde a instrução deve atuar — usar `## Quando Usar Cada Tipo` como guia:
- Afeta todo repositório → `.github/copilot-instructions.md`
- Afeta diretório específico → `.instructions.md`
- Capacidade reutilizável → `SKILL.md`
- Persona com fluxo → `.agent.md`

### 2. Escrever o frontmatter YAML

Consultar o formato correspondente em `## Formato: SKILL.md` ou `## Formato: .agent.md`.
Campos mínimos obrigatórios: `name`, `description`.

### 3. Estruturar o conteúdo

Para **Skills**: seguir as seções obrigatórias de `## Formato: SKILL.md`:
1. Quando Usar
2. Passos/Procedimento
3. Exemplos ou Templates
4. Checklist de validação

Para **Agents**: seguir as seções obrigatórias:
1. Persona
2. Metodologia
3. Protocolo de Escalamento
4. Fluxo de Trabalho
5. Nunca Faça

### 4. Validar com checklist

Completar o `## Checklist de Qualidade` abaixo antes de commitar.

### 5. Registrar no repositório

```bash
# Posicionar o arquivo no local correto
# Para agents e skills neste repositório:
skills/.experimental/agents/nome.agent.md
skills/.experimental/skills/nome/SKILL.md
```

## Exemplos

### SKILL.md mínimo válido

```markdown
---
name: minha-skill
description: Use quando precisar [contexto de uso]. Fornece [o que entrega].
license: MIT
---

# Minha Skill

## Quando Usar

- Situação A que justifica uso
- Situação B que justifica uso

## Passos

### 1. Primeiro passo
Descrição acionável.

### 2. Segundo passo
Descrição acionável.

## Checklist de validação

- [ ] Critério 1
- [ ] Critério 2
```

### .agent.md mínimo válido

```markdown
---
name: meu-agente
description: Papel do agente. Use quando precisar [contexto].
tools: ["read", "search", "edit", "todo"]
user-invocable: true
type: agent
targets: [copilot-cli]
license: MIT
infer: true
---

# Meu Agente

## Persona

[1-3 linhas descrevendo a persona e princípio central]

## Metodologia

[Seções técnicas específicas do domínio]

## Protocolo de Escalamento

[Quando e como escalar]

## Fluxo de Trabalho

[Passos numerados]

## Nunca Faça

[Proibições explícitas]
```

---
> Source: [ATNexusLab/skills](https://github.com/ATNexusLab/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
