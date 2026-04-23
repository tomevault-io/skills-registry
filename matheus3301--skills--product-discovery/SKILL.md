---
name: product-discovery
description: Discovery de produto antes de gerar PRD. Usar quando: usuário pede pra criar um projeto/app/feature. Faz perguntas estruturadas pra entender o produto, stack, rotas, data model, regras de negócio. Gera PRD rico e detalhado pro LoopForge. Trigger: 'cria um app', 'faz um projeto', 'quero um sistema', 'desenvolve'. Use when this capability is needed.
metadata:
  author: matheus3301
---

# Product Discovery Skill

Conduzir discovery de produto através de conversa estruturada antes de gerar PRD. O objetivo é entender profundamente o que será construído para gerar stories executáveis.

## Quando Usar

**Ativar quando:**
- Usuário pede pra criar um app/sistema/projeto novo
- Usuário descreve uma feature complexa
- Antes de rodar `loopforge prd generate`
- Quando o PM vai iniciar um novo projeto

**NÃO usar quando:**
- Task simples e bem definida
- Bug fix ou ajuste pequeno
- Usuário já tem PRD pronto

## O Trabalho

1. Receber descrição inicial do usuário
2. Fazer **5-10 perguntas clarificadoras** em lotes de até 5
3. Mapear completamente: problema, stack, telas, dados, regras
4. Gerar PRD rico e detalhado
5. Salvar e iniciar build com LoopForge

**IMPORTANTE:** Não implementar nada. Só gerar o PRD após discovery completo.

---

## Fase 1: Perguntas de Discovery

Mesmo que a descrição pareça clara, SEMPRE fazer perguntas. O objetivo é capturar detalhes que evitam retrabalho.

### Dimensões a Cobrir

| Dimensão | O que descobrir |
|----------|-----------------|
| **Problema** | Que dor resolve? Pra quem? |
| **Core** | Funcionalidades essenciais (o que FAZ) |
| **Scope** | O que NÃO faz (evita scope creep) |
| **Sucesso** | Como saber que tá pronto? |
| **Stack** | Framework, DB, auth, hosting |
| **Telas** | Rotas principais, layout, navegação |
| **Dados** | Entidades, relacionamentos, campos |
| **Regras** | Lógica de negócio, cálculos, edge cases |
| **Quality Gates** | Comandos que DEVEM passar |

### Formato das Perguntas

Usar opções letradas pra facilitar resposta rápida:

```
1. Qual o principal problema que esse app resolve?
   A. [opção específica ao contexto]
   B. [opção específica ao contexto]
   C. [opção específica ao contexto]
   D. Outro: [especificar]

2. Quem é o usuário principal?
   A. Usuário final (consumidor)
   B. Usuário interno (funcionário)
   C. Admin/backoffice
   D. API (outro sistema)

3. Qual stack vamos usar?
   A. Rails 8 + PostgreSQL
   B. Rails 8 + SQLite
   C. Next.js + PostgreSQL
   D. Outro: [especificar]

4. Quais quality gates devem passar?
   A. Testes (rspec, jest, etc)
   B. Lint (rubocop, eslint)
   C. Build/compile
   D. Nenhum por enquanto
   E. Outro: [especificar]

5. É projeto novo ou codebase existente?
   A. Projeto novo do zero
   B. Codebase existente (adicionar feature)
```

### Perguntas Obrigatórias

SEMPRE perguntar:
- [ ] É projeto **novo** ou **existente**?
- [ ] Qual **stack** (framework, DB)?
- [ ] Quais **quality gates** funcionam no ambiente?
- [ ] Quais as **telas/rotas** principais?
- [ ] Qual o **data model** básico?

---

## Fase 2: Estrutura do PRD

Após coletar respostas, gerar JSON com esta estrutura:

```json
{
  "version": 1,
  "project": "Nome do Projeto",
  "overview": "Resumo do problema + solução em 2-3 frases",
  "goals": [
    "Objetivo específico e mensurável 1",
    "Objetivo específico e mensurável 2"
  ],
  "nonGoals": [
    "O que explicitamente NÃO será feito"
  ],
  "successMetrics": [
    "Como medir sucesso"
  ],
  "openQuestions": [
    "Dúvidas restantes pra resolver depois"
  ],
  "stack": {
    "framework": "Rails 8",
    "language": "Ruby",
    "database": "SQLite",
    "auth": "Devise",
    "frontend": "Hotwire/Turbo",
    "css": "Tailwind"
  },
  "routes": [
    { "path": "/", "name": "Home", "purpose": "Landing/dashboard" },
    { "path": "/projects", "name": "Projects", "purpose": "CRUD de projetos" }
  ],
  "uiNotes": [
    "Usar Tailwind com tema dark",
    "Mobile-first"
  ],
  "dataModel": [
    {
      "entity": "User",
      "fields": ["id", "email", "name", "created_at"],
      "relationships": ["has_many :projects"]
    },
    {
      "entity": "Project",
      "fields": ["id", "user_id", "title", "description", "status"],
      "relationships": ["belongs_to :user"]
    }
  ],
  "rules": [
    "Usuário só vê seus próprios projetos",
    "Status pode ser: draft, active, archived"
  ],
  "qualityGates": [
    "bundle exec rspec",
    "bundle exec rubocop"
  ],
  "stories": [
    {
      "id": "US-001",
      "title": "Setup inicial do projeto",
      "status": "open",
      "dependsOn": [],
      "description": "Criar projeto Rails 8 com SQLite e configurações básicas",
      "acceptanceCriteria": [
        "rails new executado com flags corretas",
        "Gemfile inclui: devise, tailwindcss-rails",
        "bin/dev funciona e abre na porta 3000",
        "Exemplo: rails s roda sem erros",
        "Negativo: não incluir gems desnecessárias"
      ]
    }
  ]
}
```

### Regras para Stories

- **IDs**: Sequenciais (`US-001`, `US-002`, ...)
- **Status**: Sempre `"open"` pra novas
- **dependsOn**: IDs das stories que precisam estar prontas
- **Acceptance Criteria**: 
  - Específico e verificável
  - Incluir pelo menos 1 exemplo positivo
  - Incluir pelo menos 1 caso negativo/edge case
- **Tamanho**: Cada story deve ser completável em **uma iteração** do LoopForge (5-15 min)

### Ordem das Stories

1. **Setup** (se projeto novo): scaffold, gems, configs
2. **Data model**: migrations, models, relationships
3. **Auth** (se aplicável): Devise ou similar
4. **Core features**: funcionalidades principais
5. **UI/Polish**: melhorias visuais, UX
6. **Extras**: features secundárias

---

## Fase 3: Output

Após gerar o PRD:

1. Salvar em `.loopforge/prd.json`
2. Mostrar resumo pro usuário:

```
📋 PRD Gerado: {projeto}

📊 Resumo:
- {N} stories
- Stack: {framework} + {db}
- Quality Gates: {gates}

🎯 Próximo passo:
Quer que eu inicie o build com LoopForge?
```

3. Se usuário confirmar, seguir skill `loopforge-workflow`

---

## Exemplo de Conversa

**Usuário:** "Cria um app pra gerenciar minhas receitas de comida"

**Pip (Discovery):**
```
Boa! Antes de começar, algumas perguntas rápidas:

1. Qual o principal objetivo?
   A. Salvar receitas pessoais pra consultar depois
   B. Compartilhar receitas com outras pessoas
   C. Planejar refeições da semana
   D. Outro: [especificar]

2. É projeto novo ou já existe código?
   A. Novo do zero
   B. Existente

3. Qual stack prefere?
   A. Rails 8 + SQLite (simples, rápido)
   B. Rails 8 + PostgreSQL (mais robusto)
   C. Next.js + Prisma
   D. Outro: [especificar]

4. Precisa de autenticação (login)?
   A. Sim, cada usuário suas receitas
   B. Não, app pessoal sem login
   C. Sim, com login social (Google)

5. Quality gates que funcionam aqui?
   A. rspec (testes)
   B. rubocop (lint)
   C. Nenhum por enquanto
```

**Usuário:** "A, A, A, A, C"

**Pip:**
```
Entendi! Mais algumas:

6. Quais campos uma receita tem?
   A. Básico: título, ingredientes, modo de preparo
   B. Completo: + tempo, porções, categoria, foto
   C. Bem completo: + nutrição, custo, dificuldade
   D. Me sugere

7. Quais telas principais?
   A. Lista + detalhe + form (CRUD básico)
   B. + busca e filtros
   C. + dashboard com favoritos
   D. Me sugere

8. Quer importar receitas de algum lugar?
   A. Não, só cadastro manual
   B. Sim, de URL (scraping)
   C. Sim, de arquivo JSON/CSV
```

**Usuário:** "B, C, A"

**Pip:** *gera PRD completo e oferece iniciar build*

---

## Integração com LoopForge Workflow

Após PRD gerado:
1. Salvar em `.loopforge/prd.json`
2. Perguntar se quer iniciar build
3. Se sim, seguir `loopforge-workflow`:
   - `nohup loopforge build &`
   - Criar cron de monitoramento
   - Notificar quando completo (usar `message` tool!)

---

## Anti-Patterns a Evitar

❌ **Não fazer:**
- Gerar PRD sem perguntas
- Aceitar "faz um app de X" e sair gerando
- Stories vagas tipo "Implementar funcionalidade principal"
- Quality gates que não funcionam no ambiente
- Ignorar stack/DB do usuário

✅ **Fazer:**
- Sempre perguntar, mesmo se parecer claro
- Stories específicas e verificáveis
- Quality gates testados
- Data model explícito
- Rotas mapeadas

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matheus3301) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
