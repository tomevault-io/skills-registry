---
name: tech-lead
description: Decisões técnicas de arquitetura e stack. Usar quando: precisa definir tecnologias, arquitetura, padrões de código, ou escolher skills para um projeto. Trigger: 'qual stack', 'como arquitetar', 'que tecnologia usar'. Use when this capability is needed.
metadata:
  author: matheus3301
---

# Tech Lead Skill

Tomar decisões técnicas sólidas baseadas no contexto do projeto.

## Responsabilidades

1. **Escolher Stack** - Framework, DB, auth, hosting
2. **Definir Arquitetura** - Estrutura, patterns, separação
3. **Selecionar Skills** - Quais skills o projeto precisa
4. **Code Standards** - Padrões, linting, testes
5. **Quality Gates** - Comandos que devem passar

---

## Decision Framework: Stack

### Web App (Frontend + Backend)

| Critério | Rails 8 | Next.js | 
|----------|---------|---------|
| Velocidade de dev | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Real-time | Hotwire/Turbo | WebSockets/Pusher |
| API | Built-in | API Routes |
| Auth | Devise (fácil) | NextAuth (médio) |
| Admin | Rails Admin | Custom |
| Melhor para | CRUD, SaaS interno | SaaS público, landing |

**Recomendação padrão:** Rails 8 + Hotwire + Tailwind
- Matheus conhece Rails
- Rápido pra prototipar
- SQLite pra dev (sem setup)

### Database

| Critério | SQLite | PostgreSQL | 
|----------|--------|------------|
| Setup | Zero | Precisa instalar |
| Dev local | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| Produção | Litestream | Supabase/Neon |
| Melhor para | MVP, projetos pessoais | SaaS, multi-tenant |

**Recomendação padrão:** SQLite pra dev, PostgreSQL pra prod

### Auth

| Opção | Quando usar |
|-------|-------------|
| Devise (Rails) | Padrão pra Rails |
| NextAuth | Padrão pra Next.js |
| Clerk/Auth0 | Se precisa social login fácil |
| Nenhum | App pessoal single-user |

---

## Decision Framework: Skills para Projeto

### Matriz de Decisão

```
Projeto tem UI? 
  └─ SIM → frontend-design (OBRIGATÓRIO)
  
Projeto é React/Next.js?
  └─ SIM → react-best-practices
  
Projeto tem banco de dados?
  └─ PostgreSQL → postgres-best-practices
  
Projeto tem pagamentos?
  └─ SIM → stripe-best-practices
  
Projeto é SaaS público?
  └─ SIM → copywriting, pricing-strategy, launch-strategy
  
Projeto tem landing page?
  └─ SIM → page-cro, seo-audit, copywriting
  
Projeto precisa de copy elegante?
  └─ SIM → beautiful-prose
```

### Bundles Prontos

**Bundle: SaaS Starter**
```
frontend-design
react-best-practices (ou Rails)
postgres-best-practices
copywriting
pricing-strategy
launch-strategy
```

**Bundle: Internal Tool**
```
frontend-design
postgres-best-practices
internal-comms
```

**Bundle: Landing Page**
```
frontend-design
copywriting
page-cro
seo-audit
schema-markup
```

**Bundle: E-commerce**
```
frontend-design
stripe-best-practices
postgres-best-practices
email-sequence
page-cro
```

---

## Quality Gates por Stack

### Rails 8
```json
{
  "qualityGates": [
    "bundle exec rails db:migrate",
    "bundle exec rails assets:precompile"
  ]
}
```

⚠️ **Evitar** `rspec` e `rubocop` se não configurados

### Next.js
```json
{
  "qualityGates": [
    "npm run build",
    "npm run lint"
  ]
}
```

### Genérico (qualquer projeto)
```json
{
  "qualityGates": [
    "echo 'Build check passed'"
  ]
}
```

---

## Arquitetura: Patterns Recomendados

### Rails 8
```
app/
├── controllers/     # Thin controllers
├── models/          # Fat models (business logic)
├── views/           # ERB + Hotwire
├── components/      # ViewComponents
├── services/        # Service objects (se complexo)
└── jobs/            # Background jobs
```

### Next.js (App Router)
```
app/
├── (auth)/          # Route groups
├── api/             # API routes
├── components/      # React components
├── lib/             # Utilities
└── actions/         # Server actions
```

---

## Output: Configuração do Projeto

Após decisões, gerar:

### 1. Stack Summary
```yaml
stack:
  framework: Rails 8
  language: Ruby 3.3
  database: SQLite (dev) / PostgreSQL (prod)
  auth: Devise
  frontend: Hotwire + Turbo
  css: Tailwind CSS
  hosting: TBD
```

### 2. Skills para Instalar
```bash
# Copiar para .claude/skills/
cp -r ~/www/skills/frontend-design .claude/skills/
cp -r ~/www/skills/postgres-best-practices .claude/skills/
```

### 3. Quality Gates
```json
{
  "qualityGates": [
    "bundle exec rails db:migrate"
  ]
}
```

### 4. CLAUDE.md Template
```markdown
# {Projeto}

## Stack
Rails 8 + SQLite + Devise + Tailwind

## Skills
- frontend-design: UI distintiva
- postgres-best-practices: Boas práticas de DB

## Guidelines
- Usar Hotwire pra interatividade
- Mobile-first
- Dark mode opcional
```

---

## Perguntas para Definir Stack

Se não está claro, perguntar ao cliente:

1. "Você prefere Rails ou Next.js? (Rails é mais rápido pra gente)"

2. "Precisa de login/usuários?"

3. "Vai ter pagamentos?"

4. "É pra você usar ou vai ter outros usuários?"

5. "Precisa funcionar offline ou sempre online tá ok?"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matheus3301) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
