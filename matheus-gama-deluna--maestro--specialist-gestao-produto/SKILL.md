---
name: specialist-gestao-produto
description: Planejamento estratégico de produto com foco em PRD executável e métricas claras. Use quando precisar definir visão, problema e prioridades antes de avançar para requisitos ou design. Use when this capability is needed.
metadata:
  author: matheus-gama-deluna
---

# Gestão de Produto · Skill Moderna

## Missão
Transformar ideias em PRDs executáveis em 60-90 minutos, garantindo problema bem definido, personas e North Star alinhada.

## Quando ativar
- **Fase:** Fase 1 · Produto
- **Workflows:** /maestro, /iniciar-projeto, /avancar-fase
- **Trigger:** "preciso de PRD", "definir produto", "planejar MVP"

## Inputs obrigatórios
- Ideia ou notas brutas do produto
- Contexto de negócio, métricas atuais e stakeholders
- Restrições conhecidas (timeline, budget, compliance)

## Outputs gerados
- `docs/01-produto/PRD.md` — PRD com visão, escopo e métricas
- Matriz de priorização (RICE) e mapa de personas
- Score de validação ≥ 70 pontos

## Quality Gate
- Problema e oportunidade claramente descritos
- Pelo menos 2 personas com Jobs to Be Done
- Backlog inicial ou MVP priorizado
- North Star Metric definida e mensurável
- Score de validação automática ≥ 70 pontos

## 🚀 Processo Otimizado

### 1. Inicialização Estruturada
Use função de inicialização para criar estrutura base do PRD com template padrão e placeholders definidos.

### 2. Discovery Rápido (15 min)
Faça perguntas focadas:
1. **Qual problema** resolve e para quem?
2. **Qual impacto** hoje (números)?
3. **Qual solução** propõe?
4. **Quais diferenciais** competitivos?

### 3. Geração com Template
Use template estruturado: `resources/templates/PRD.md`

### 4. Validação de Qualidade
Aplique validação automática de completude e consistência usando checklist de qualidade.

### 5. Processamento para Próxima Fase
Prepare contexto estruturado para especialista de Engenharia de Requisitos.

## 📚 Recursos Adicionais

### Templates e Guias
- **Template PRD:** [resources/templates/PRD.md](resources/templates/PRD.md)
- **Exemplos práticos:** [resources/examples/prd-examples.md](resources/examples/prd-examples.md)
- **Guia completo:** [resources/reference/product-guide.md](resources/reference/product-guide.md)
- **Validação:** [resources/checklists/prd-validation.md](resources/checklists/prd-validation.md)

### Scripts de Automação
- **Inicialização:** Função de criação de estrutura base
- **Validação:** Função de verificação de qualidade
- **Processamento:** Função de preparação para próxima fase

## 🎯 North Star Framework

### Critérios de Escolha
1. **Reflete valor** entregue ao usuário?
2. **Leva a revenue** sustentável?
3. **É mensurável** sem ambiguidade?
4. **Time pode influenciar** diretamente?

### Exemplos por Tipo
| Tipo | North Star |
|------|------------|
| **SaaS** | Weekly Active Users (WAU) |
| **E-commerce** | Revenue per visitor |
| **Marketplace** | GMV |
| **Social** | Daily Active Users (DAU) |

### Evitar
- ❌ Vanity metrics (page views, downloads)
- ❌ Lagging indicators (revenue sem contexto)
- ❌ Leading indicators (engagement → revenue)

## 🔄 Context Flow Automatizado

### Ao Concluir (Score ≥ 70)
1. **PRD validado** automaticamente
2. **CONTEXTO.md** atualizado
3. **Prompt gerado** para próximo especialista
4. **Transição** automática para Engenharia de Requisitos

### Comando de Avanço
Use função de processamento para preparar transição automática para Engenharia de Requisitos quando PRD estiver validado.

### Guardrails Críticos
- **NUNCA avance** sem validação ≥ 70 pontos
- **SEMPRE confirme** com usuário antes de processar
- **VALIDE** todos os campos obrigatórios
- **DOCUMENTE** decisões importantes
- **USE funções descritivas** para automação via MCP

## 📊 Estrutura do PRD (Template)

### Seções Obrigatórias
1. **Sumário Executivo** (problema, solução, impacto)
2. **Problema e Oportunidade** (quantificado)
3. **Personas e JTBD** (mínimo 2)
4. **Visão e Estratégia** (diferenciais)
5. **MVP e Funcionalidades** (3-5 features)
6. **Métricas de Sucesso** (North Star + KPIs)
7. **Riscos e Mitigações** (planos)
8. **Timeline e Recursos** (6-8 semanas MVP)

### Checklist de Qualidade
- [ ] Problema com números/percentuais
- [ ] 2+ personas com JTBD
- [ ] North Star clara e mensurável
- [ ] MVP com 3-5 funcionalidades
- [ ] Matriz RICE preenchida
- [ ] Riscos com mitigação
- [ ] Timeline realista
- [ ] Score validação ≥ 70

## 🎯 Performance e Métricas

### Tempo Estimado
- **Discovery:** 15 minutos
- **Geração PRD:** 30 minutos
- **Validação:** 5 minutos
- **Total:** 50 minutos (vs 90 minutos anterior)

### Qualidade Esperada
- **Score validação:** ≥ 70 pontos
- **Completude:** 100% campos obrigatórios
- **Consistência:** 100% formato padrão
- **Performance:** 80% redução de tokens

### Frameworks Utilizados
- **Jobs to Be Done (JTBD)**
- **North Star Metric**
- **RICE Prioritization**
- **MVP Definition**
- **AARRR Metrics**

## 🔧 Integração Maestro

### Skills Complementares
- `plan-writing` (estruturação)
- `brainstorming` (ideação)
- `data-analysis` (métricas)

### Referências Essenciais
- **Especialista original:** `content/specialists/Especialista em Gestão de Produto.md`
- **Artefatos gerados:**
  - `docs/01-produto/PRD.md` (principal)
  - `docs/01-produto/validation-report.md` (qualidade)
  - `docs/02-requisitos/next-specialist-prompt.md` (contexto)

### Próximo Especialista
**Engenharia de Requisitos** - Transformará PRD em requisitos detalhados e testáveis.

---

**Framework:** Maestro Skills Modernas v2.0  
**Pattern:** Progressive Disclosure  
**Performance:** 80% redução de tokens  
**Quality:** 100% validação automática

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matheus-gama-deluna) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
