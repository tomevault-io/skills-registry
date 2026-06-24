---
name: docker-compose-security
description: Regras rígidas de segurança para Docker Compose - NUNCA expor variáveis de ambiente Use when this capability is needed.
metadata:
  author: lucasbiason
---

# Docker Compose - Regras de Segurança

**REGRAS RÍGIDAS E OBRIGATÓRIAS para Docker Compose.**

---

## Regra de Ouro: NUNCA Expor Variáveis

### ❌ PROIBIDO (INADMISSÍVEL)

```yaml
services:
  app:
    environment:
      - SERVER_TYPE=http
      - SERVER_PORT=8080
      - AUTHENTICATION_TYPE=apikey
      - AUTHENTICATION_API_KEY=${EVOLUTION_API_KEY:-ChangeMeToSecureKey123!}
      - DATABASE_ENABLED=true
      - DATABASE_PROVIDER=postgresql
      - DATABASE_CONNECTION_URI=${EVOLUTION_DATABASE_URI:-postgresql://user:password@127.0.0.1:5433/db}
```

**NUNCA, JAMAIS, EM HIPÓTESE ALGUMA:**
- ❌ Expor senhas no docker-compose.yml
- ❌ Expor usuários no docker-compose.yml
- ❌ Expor tokens/API keys no docker-compose.yml
- ❌ Expor URIs de conexão com credenciais no docker-compose.yml
- ❌ Expor configurações sensíveis no docker-compose.yml
- ❌ Usar valores padrão inseguros (ex: `ChangeMeToSecureKey123!`)

### ✅ CORRETO (OBRIGATÓRIO)

```yaml
services:
  app:
    env_file:
      - .env.app
      - .env.database
    # Apenas variáveis não-sensíveis podem estar aqui
    environment:
      - NODE_ENV=production
      - LOG_LEVEL=info
```

**SEMPRE:**
- ✅ Usar `env_file:` para todas as variáveis sensíveis
- ✅ Criar arquivos `.env.*` separados por serviço
- ✅ Adicionar `.env.*` ao `.gitignore`
- ✅ Criar `.env.example` com placeholders
- ✅ Documentar variáveis necessárias no README

---

## Estrutura de Arquivos .env

```
project/
├── docker-compose.yml          # SEM variáveis sensíveis
├── .env.example                # Template com placeholders
├── .env.app                    # Variáveis da aplicação (gitignored)
├── .env.database               # Credenciais do banco (gitignored)
├── .env.redis                  # Credenciais do Redis (gitignored)
└── .gitignore                  # Deve incluir .env.*
```

### .env.example (Template Público)

```bash
# Application
SERVER_TYPE=http
SERVER_PORT=8080
AUTHENTICATION_TYPE=apikey
AUTHENTICATION_API_KEY=your_api_key_here

# Database
DATABASE_ENABLED=true
DATABASE_PROVIDER=postgresql
DATABASE_CONNECTION_URI=postgresql://user:password@host:port/database

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=your_redis_password_here
```

### .env.app (Gitignored)

```bash
# Application - NUNCA COMMITAR
SERVER_TYPE=http
SERVER_PORT=8080
AUTHENTICATION_TYPE=apikey
AUTHENTICATION_API_KEY=sk_live_abc123xyz789
```

---

## Validação Obrigatória

**Antes de commitar docker-compose.yml:**

1. Verificar se há variáveis sensíveis em `environment:`
2. Verificar se `env_file:` está configurado
3. Verificar se `.env.*` está no `.gitignore`
4. Verificar se `.env.example` existe com placeholders

**Script de validação:**
```bash
# Verificar se há senhas/tokens no docker-compose.yml
grep -E "(password|secret|key|token|uri|connection)" docker-compose.yml | grep -v "env_file"
```

Se encontrar algo, **ERRO CRÍTICO** - não commitar!

---

## Checklist

- [ ] Nenhuma variável sensível em `environment:`
- [ ] Todas as variáveis sensíveis em `env_file:`
- [ ] Arquivos `.env.*` no `.gitignore`
- [ ] `.env.example` criado com placeholders
- [ ] README documenta variáveis necessárias
- [ ] Validação passou (script acima)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucasbiason) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
