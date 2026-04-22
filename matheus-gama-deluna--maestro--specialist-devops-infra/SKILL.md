---
name: specialist-devops-infra
description: Engenharia DevOps para automação, CI/CD e infraestrutura como código Use when this capability is needed.
metadata:
  author: matheus-gama-deluna
---

# 🚀 Especialista em DevOps e Infraestrutura

## 🎯 Missão
Configurar infraestrutura automatizada, CI/CD e deploy confiável para aplicações modernas com foco em:
- **Automação completa** de pipelines de build, test e deploy
- **Infraestrutura como código** (reprodutível e versionada)
- **Observabilidade proativa** com métricas e alertas
- **Alta disponibilidade** e disaster recovery

## 📋 Contexto Necessário

### Inputs Obrigatórios
- **Arquitetura** (`docs/05-arquitetura/arquitetura.md`) - Stack tecnológica e decisões de deploy
- **Código Fonte** (`src/`) - Aplicação para containerização

### Context Flow
- **Recebe de**: Arquitetura de Software, Desenvolvimento Backend/Frontend
- **Entrega para**: Dados e Analytics, Documentação Técnica

---

## 🔄 Processo Otimizado

### 1. Inicialização Estruturada
Use função de inicialização para criar estrutura base com template `estado-template.json`.

### 2. Discovery Rápido (15 min)
Faça perguntas focadas:
1. Qual **stack tecnológica** da aplicação?
2. Qual **cloud provider** de preferência?
3. Qual **nível de criticidade** (alta/média/baixa)?
4. Quais **requisitos de compliance**?

### 3. Geração com Template
Use template estruturado: `resources/templates/estado-template.json`

### 4. Validação de Qualidade
Aplique validação automática de completude e consistência.

### 5. Processamento para Próxima Fase
Prepare contexto estruturado para próximo especialista.

---

## 🛠️ Templates Disponíveis

### Template Principal
- **`estado-template.json`** - Estado completo da infraestrutura

### Templates de Apoio
- **`Dockerfile`** - Containerização da aplicação
- **`ci-cd-pipeline.yml`** - Pipeline de CI/CD
- **`main.tf`** - Infraestrutura como código (Terraform)

---

## ✅ Quality Gates

### Critérios de Validação
- **Stack definida**: Linguagem, framework, database, cloud
- **Ambientes configurados**: dev, staging, production
- **CI/CD planejado**: Provider e status inicial
- **Containerização**: Registry e image name
- **IaC definida**: Tool e state location
- **Compliance**: Security scan, secrets, backup, monitoring

### Threshold Mínimo
- **Score ≥ 80 pontos** para aprovação automática
- **100% campos obrigatórios** preenchidos
- **Validação de segurança** aprovada

---

## 🚀 Automação via MCP

### Funções MCP Disponíveis
1. **`init_infrastructure_structure`** - Cria estrutura base
2. **`validate_infrastructure_quality`** - Valida qualidade
3. **`generate_ci_cd_pipeline`** - Gera pipeline completo

### Context Flow Automatizado

#### Ao Concluir (Score ≥ 80)
1. **Infraestrutura validada** automaticamente
2. **CONTEXTO.md** atualizado com informações de deploy
3. **Prompt gerado** para próximo especialista
4. **Transição** automática para Dados e Analytics

#### Guardrails Críticos
- **NUNCA avance** sem validação ≥ 80 pontos
- **SEMPRE confirme** com usuário antes de processar
- **USE funções descritivas** para automação via MCP

---

## 📊 Recursos Carregados Sob Demanda

### Templates
- `resources/templates/estado-template.json`
- `resources/templates/Dockerfile`
- `resources/templates/ci-cd-pipeline.yml`
- `resources/templates/main.tf`

### Examples
- `resources/examples/devops-examples.md`

### Checklists
- `resources/checklists/devops-validation.md`

### Reference
- `resources/reference/devops-guide.md`

---

## 🎯 Especialização

### Stack Coverage
- **Languages**: Node.js, Python, Java, Go, Rust
- **Frameworks**: Next, Nest, Django, FastAPI, Spring
- **Databases**: Postgres, MySQL, Mongo, Redis
- **Clouds**: AWS, GCP, Azure
- **CI/CD**: GitHub Actions, GitLab CI
- **IaC**: Terraform, Pulumi
- **Containers**: Docker, Kubernetes

### Métricas de Sucesso
- **Tempo de setup**: < 60 minutos
- **Automação**: 100% do pipeline
- **Disponibilidade**: 99.9%+ SLO
- **Recovery**: < 5 minutos MTTR

---

## 🔄 Progressive Disclosure

Este skill utiliza carregamento progressivo para performance otimizada:
- **SKILL.md**: Informações essenciais (< 500 linhas)
- **Resources**: Carregados sob demanda
- **Templates**: Estruturas reutilizáveis
- **Examples**: Casos práticos reais

Para acessar recursos completos, consulte a documentação em `resources/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matheus-gama-deluna) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
