---
name: docker-entrypoint-pattern
description: Padrão para criar entrypoint.sh com múltiplos comandos para containers Docker, permitindo execução flexível de diferentes operações. Use when this capability is needed.
metadata:
  author: lucasbiason
---

# Docker Entrypoint Pattern

**Padrão para criar `entrypoint.sh` com múltiplos comandos, permitindo execução flexível de diferentes operações em containers Docker.**

---

## Quando Usar

Aplicar esta skill quando:
- Criando ou modificando Dockerfiles
- Precisando de múltiplos comandos no container (dev, test, prod, migrate, etc.)
- Querendo flexibilidade para executar diferentes operações sem modificar Dockerfile
- Precisando de inicialização antes de iniciar a aplicação (migrations, health checks, etc.)

---

## Por Que Usar Entrypoint.sh?

### Vantagens

1. **Flexibilidade**: Um único Dockerfile pode executar múltiplos comandos
2. **Inicialização**: Executar tarefas antes de iniciar a aplicação (migrations, seed, health checks)
3. **Reutilização**: Mesmo container para dev, test, prod
4. **Sinais Unix**: `exec "$@"` garante que o processo principal receba SIGTERM corretamente
5. **Manutenibilidade**: Lógica de inicialização centralizada em um script

### Padrão Docker

```dockerfile
# Copiar entrypoint
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Usar como entrypoint
ENTRYPOINT ["/entrypoint.sh"]
CMD ["runserver"]  # Comando padrão
```

**Uso:**
```bash
# Executar comando padrão
docker run myapp

# Executar comando específico
docker run myapp test
docker run myapp migrate
docker run myapp dev
```

---

## Estrutura Padrão do Entrypoint

### Template Base

```bash
#!/usr/bin/env bash

# Exit immediately if a command exits with a non-zero status
# Treat unset variables as errors
# Fail on pipeline errors
set -euo pipefail

# Function to display CLI help
cli_help() {
  local cli_name=${0##*/}
  echo "
$cli_name
[Project Name] - Entrypoint CLI
Usage: $cli_name [command]

Commands:
  dev           Start development server
  prod          Start production server
  test          Run tests with coverage
  migrate       Apply database migrations
  health        Check service health
  *             Display this help message

Environment Variables:
  PORT          Server port (default: 8000)
  WORKERS       Number of workers (default: 4)
  LOG_LEVEL     Logging level (default: info)
"
  exit 1
}

# Main command handler
case "${1:-help}" in
  dev)
    # Development server
    ;;
  prod|runserver)
    # Production server
    ;;
  test)
    # Run tests
    ;;
  migrate)
    # Run migrations
    ;;
  health)
    # Health check
    ;;
  *)
    cli_help
    ;;
esac
```

---

## Regras Obrigatórias

1. **Sempre usar `set -euo pipefail`**:
   - `set -e`: Para ao primeiro erro
   - `set -u`: Erro se variável não definida
   - `set -o pipefail`: Falha se qualquer comando em pipeline falhar

2. **Sempre usar `exec "$@"` no final**:
   - Substitui o processo shell pelo processo principal
   - Garante que sinais Unix (SIGTERM) cheguem ao processo correto
   - Evita problemas com PID 1

3. **Sempre tornar executável**:
   ```dockerfile
   RUN chmod +x /entrypoint.sh
   ```

4. **Sempre usar formato exec no ENTRYPOINT**:
   ```dockerfile
   ENTRYPOINT ["/entrypoint.sh"]  # ✅ Correto
   # NÃO usar: ENTRYPOINT /entrypoint.sh  # ❌ Errado
   ```

5. **Sempre validar variáveis críticas**:
   ```bash
   if [ -z "${SECRET_KEY:-}" ]; then
     echo "ERROR: SECRET_KEY not set"
     exit 1
   fi
   ```

---

## Templates por Stack

### Django

**Referência:** `core/templates/entrypoint/django-entrypoint.sh`

**Comandos típicos:**
- `migrate` - Aplicar migrations
- `test` - Executar testes
- `dev` - Django runserver
- `prod` - Gunicorn
- `collectstatic` - Coletar arquivos estáticos

### FastAPI

**Referência:** `core/templates/entrypoint/fastapi-entrypoint.sh`

**Comandos típicos:**
- `migrate` - Alembic upgrade head
- `test` - Executar testes
- `dev` - Uvicorn com reload
- `prod` - Uvicorn multi-worker
- `health` - Health check

### Node.js (Backend)

**Referência:** `core/templates/entrypoint/nodejs-entrypoint.sh`

**Comandos típicos:**
- `test` - Executar testes
- `dev` - Nodemon ou ts-node-dev
- `prod` - Node dist/index.js
- `seed` - Executar seed scripts
- `health` - Health check

### React.js (Frontend)

**Referência:** `core/templates/entrypoint/react-entrypoint.sh`

**Comandos típicos:**
- `dev` - Vite/Next.js dev server
- `build` - Build de produção
- `prod` - Servir build estático (nginx)
- `test` - Executar testes

---

## Boas Práticas

### 1. Aguardar Dependências

```bash
wait_for_service() {
  local host=$1
  local port=$2
  local service=$3
  
  echo "⏳ Waiting for $service at $host:$port..."
  until nc -z "$host" "$port"; do
    echo "$service is unavailable - sleeping"
    sleep 2
  done
  echo "✅ $service is ready!"
}

# Aguardar dependências antes de iniciar
wait_for_service db 5432 "PostgreSQL"
wait_for_service redis 6379 "Redis"
```

### 2. Validação de Variáveis

```bash
# Validar variáveis críticas
REQUIRED_VARS=("DATABASE_URL" "SECRET_KEY")
for var in "${REQUIRED_VARS[@]}"; do
  if [ -z "${!var:-}" ]; then
    echo "ERROR: $var not set"
    exit 1
  fi
done
```

### 3. Logging Estruturado

```bash
log() {
  echo "[$(date +'%Y-%m-%d %H:%M:%S')] $1"
}

log "Starting application..."
log "Port: ${PORT:-8000}"
log "Environment: ${ENVIRONMENT:-development}"
```

### 4. Health Checks

```bash
health)
  # Verificar dependências
  check_database || exit 1
  check_redis || exit 1
  echo "✅ All services healthy"
  exit 0
  ;;
```

### 5. Condicionais por Ambiente

```bash
# Rodar migrations apenas se necessário
if [ "${RUN_MIGRATIONS:-false}" = "true" ]; then
  echo "📦 Running migrations..."
  alembic upgrade head
fi

# Coletar estáticos apenas em produção
if [ "${ENVIRONMENT:-development}" = "production" ]; then
  echo "📁 Collecting static files..."
  python manage.py collectstatic --noinput
fi
```

---

## Integração com Dockerfile

### Padrão Multi-Stage

```dockerfile
FROM python:3.11-slim AS base

WORKDIR /app

# Instalar dependências
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copiar código
COPY . .

# Copiar entrypoint
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Expor porta
EXPOSE 8000

# Entrypoint e CMD
ENTRYPOINT ["/entrypoint.sh"]
CMD ["prod"]  # Comando padrão
```

### Uso no Docker Compose

```yaml
services:
  backend:
    build: .
    entrypoint: ["/entrypoint.sh"]
    command: ["dev"]  # Override CMD
    environment:
      - PORT=8000
      - RUN_MIGRATIONS=true
```

---

## Checklist

- [ ] `set -euo pipefail` no início
- [ ] Função `cli_help()` implementada
- [ ] `exec "$@"` usado no final de cada comando
- [ ] Entrypoint tornando executável no Dockerfile
- [ ] Validação de variáveis críticas
- [ ] Aguardar dependências (se necessário)
- [ ] Logging estruturado
- [ ] Health check implementado
- [ ] Comandos documentados no help
- [ ] Testado em dev e prod

---

## Referências

- **Templates:** `core/templates/entrypoint/`
- **Django Template:** `core/templates/django/entrypoint.sh`
- **DevOps Docs:** `core/docs/programming/devops.md` (seção entrypoint)
- **Docker Best Practices:** Sempre usar `exec "$@"` para sinais Unix

---

**Última Atualização:** 2026-01-21

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucasbiason) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
