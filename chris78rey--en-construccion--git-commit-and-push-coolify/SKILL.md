---
name: git-commit-and-push-coolify
description: Automatiza el flujo completo de commit y push a GitHub con validaciones Coolify obligatorias, incluyendo detección de cambios, mensaje estructurado, y confirmación de despliegue automático. Use when this capability is needed.
metadata:
  author: chris78rey
---

# Skill: Git Commit y Push Automático para Coolify

## Propósito

Automatizar el **flujo completo de commit, push y despliegue** en Coolify sin intervención manual:

1. **Detectar archivos modificados** en el repositorio
2. **Validar cambios contra reglas Coolify** (no permita problemas conocidos)
3. **Generar mensaje de commit estructurado** (conventional commits)
4. **Hacer commit y push** automáticamente a GitHub
5. **Confirmar que webhook llegó a Coolify** y despliegue inició
6. **Monitorear despliegue** hasta estado "running"

---

## Flujo Completo (7 Pasos)

### PASO 1: Detección de Cambios

Antes de hacer nada, verificar qué archivos fueron modificados:

```bash
# Ver archivos no staged
git status

# Ver cambios detallados
git diff --stat

# Mostrar qué fue añadido/modificado/eliminado
git status --porcelain
```

**Análisis automático:**
- ✅ ¿Hay archivos no uncommitted?
- ✅ ¿Alguno es `.env` o secreto? (DETENER si es así)
- ✅ ¿Se modificó `docker-compose.yml`?
- ✅ ¿Se modificó código de aplicación?
- ✅ ¿Se modificó Dockerfile?

**Si no hay cambios:** "No hay cambios para commitear. ¿Deseas hacer otro task?"

---

### PASO 2: Pre-Commit Validation (Checklist 8 items)

Ejecutar **obligatoriamente** antes de commit:

- [ ] ✅ `git status` muestra archivos esperados (no archivos accidentales)
- [ ] ✅ `.env` NO está en cambios (debe estar en `.gitignore`)
- [ ] ✅ No hay credenciales visibles en `git diff`
- [ ] ✅ `docker-compose config` valida sintaxis YAML
- [ ] ✅ No hay `:latest` en imágenes (grep validación)
- [ ] ✅ No hay `ports:` expuestos en servicios web (si aplica)
- [ ] ✅ Variables documentadas en `.env.example` (si hay nuevas)
- [ ] ✅ Commit message es estructurado (conventional commits)

**Si algo falla:** Mostrar el error específico y pedir confirmación antes de continuar.

---

### PASO 3: Generación de Mensaje de Commit

Basado en cambios detectados, generar **conventional commit message**:

```
TIPOS DE COMMIT:
- feat: nueva feature o funcionalidad
- fix: corrección de bug
- refactor: refactorización sin cambio funcional
- perf: mejora de performance
- chore: cambios de configuración, deps, etc.
- docs: solo documentación
- ci: cambios en CI/CD o workflows
- build: cambios en Dockerfile o build process
```

**Ejemplo de análisis automático:**

```bash
# Si se modificó docker-compose.yml
git diff docker-compose.yml | grep "services:" > /dev/null && echo "Type: chore (docker-compose)"

# Si se modificó Dockerfile
git diff Dockerfile | grep "FROM" > /dev/null && echo "Type: build"

# Si se modificó código en src/
git diff src/ | grep -E "\.js|\.ts|\.jsx|\.tsx" > /dev/null && echo "Type: feat/fix (aplicación)"
```

**Generar mensaje estructurado:**

```
feat(docker-compose): agregar servicio Redis con volumen persistente

- Agrega Redis 7.0-alpine con healthcheck
- Configura volumen redis_data para persistencia
- Enlaza con API mediante environment variable REDIS_URL
- Documenta en .env.example REDIS_PASSWORD
- Actualiza README con instrucciones de nueva variable

BREAKING CHANGE: REDIS_URL ahora es obligatoria en .env
```

**O más simple si es pequeño:**

```
fix: corregir healthcheck en PostgreSQL

- Cambia test de healthcheck a pg_isready
- Agrega start_period de 10s
- Verifica en Coolify que DB inicia correctamente
```

---

### PASO 4: Stage y Commit

```bash
# Stage automático de archivos relevantes
git add docker-compose.yml Dockerfile .env.example src/ README.md

# O si el usuario prefiere seleccionar
git add <archivos específicos>

# Commit con mensaje generado
git commit -m "feat(docker-compose): agregar Redis" \
  -m "- Agrega Redis 7.0-alpine
- Configura volumen persistente
- Documenta variable REDIS_URL"

# Verificar commit creado
git log -1 --stat
```

**Validación post-commit:**
- ✅ Commit fue creado
- ✅ Mensaje es estructurado
- ✅ Git log muestra cambios correctamente

---

### PASO 5: Push a GitHub con Versionado

```bash
# Determinar rama actual
BRANCH=$(git rev-parse --abbrev-ref HEAD)
echo "Rama actual: $BRANCH"

# Validar que rama es válida (main, develop, feature/*)
if [[ "$BRANCH" != "main" && "$BRANCH" != "develop" && "$BRANCH" != "feature/"* ]]; then
  echo "⚠️ Rama inusual: $BRANCH. ¿Continuar? (s/n)"
fi

# Push a remoto
git push origin $BRANCH

# Si es rama main/develop, crear tag de release
if [[ "$BRANCH" == "main" ]]; then
  VERSION=$(git describe --tags --abbrev=0 2>/dev/null || echo "v0.0.0")
  # Parsear versión y incrementar patch
  NEXT_VERSION=$(echo $VERSION | awk -F. '{print $1"."$2"."$3+1}')
  
  echo "Crear tag de release? [$NEXT_VERSION] (s/n)"
  read -r response
  if [[ "$response" == "s" ]]; then
    git tag -a $NEXT_VERSION -m "Release: $(git log -1 --pretty=%B)"
    git push origin $NEXT_VERSION
    echo "✅ Tag $NEXT_VERSION creado y pusheado"
  fi
fi
```

**Validación post-push:**
- ✅ Push fue exitoso a GitHub
- ✅ Commit visible en GitHub
- ✅ Tag creado (si aplica)

---

### PASO 6: Verificación de Webhook (GitHub → Coolify)

Una vez pusheado, verificar que GitHub notificó a Coolify:

```bash
# Esperar a que webhook llegue
echo "Esperando a que Coolify detecte cambio (máximo 30s)..."
sleep 5

# Consultar estado en Coolify (vía API o SSH)
# Opción 1: Si tienes acceso SSH a VPS
ssh root@217.216.81.73 "docker ps -a | grep myapp"

# Opción 2: Monitorear desde GitHub (Actions si están configurados)
echo "Abre GitHub para verificar Actions: https://github.com/tu-org/tu-repo/actions"

# Opción 3: Avisar al usuario para que verifique Coolify UI
echo "✅ Push completado. Verifica Coolify en http://217.216.81.73:8000"
echo "   - Debe mostrar nuevo deployment iniciado"
echo "   - Estado esperado: 'Building' o 'Running'"
```

**Indicadores de éxito:**
- ✅ GitHub muestra nuevo commit
- ✅ Coolify panel muestra "Recent Deployments" con nuevo build
- ✅ Estado: "Building" (en progreso) o "Running" (completado)

---

### PASO 7: Monitoreo de Despliegue

Esperar a que despliegue complete y validar:

```bash
# Monitoreo de Coolify (vía SSH si disponible)
ssh root@217.216.81.73 << 'EOF'
CONTAINER=$(docker ps --filter "status=running" --format "{{.Names}}" | grep myapp | head -1)

if [ -z "$CONTAINER" ]; then
  echo "❌ Contenedor no está running"
  exit 1
fi

echo "✅ Contenedor $CONTAINER está running"

# Ver logs recientes
echo "Últimos logs:"
docker logs $CONTAINER --tail 20

# Verificar healthcheck
docker inspect $CONTAINER | grep -A 5 '"Health"'

# Probar conectividad
curl -I http://localhost:8080/health || echo "⚠️ Health endpoint no respondió"
EOF
```

**Checklist post-deploy (8 items):**
- [ ] ✅ Contenedor en estado "running"
- [ ] ✅ Sin reinicios en loop (restart count = 0 o bajo)
- [ ] ✅ Healthcheck status = "healthy"
- [ ] ✅ Logs sin errores críticos
- [ ] ✅ Dominio público responde (HTTPS 200)
- [ ] ✅ TLS activo y válido
- [ ] ✅ Persistencia confirmada (datos no se perdieron)
- [ ] ✅ Versión desplegada es la esperada (en logs o endpoint)

---

## Integraciones con Otros Skills

**Antes de PASO 2 (Pre-Commit Validation):**
Automáticamente uso los skills:
- `validate-coolify-compose` → valida `docker-compose.yml`
- `validate-common-coolify-errors` → busca errores conocidos
- `validate-coolify-rules` → verifica prohibiciones

**Si hay errores:**
Mostrar lista de problemas y pedir confirmación antes de continuar:
```
❌ ERRORES ENCONTRADOS:

1. docker-compose.yml línea 45: ❌ `ports: "8080:8080"` en servicio web
   → Cambiar a `expose: ["8080"]`

2. Dockerfile línea 12: ⚠️ Imágen `node:latest` sin versión
   → Cambiar a `node:18.20.1-alpine3.18`

3. Variable faltante: REDIS_PASSWORD no está documentada en .env.example

¿Deseas:
  [1] Continuar de todas formas (no recomendado)
  [2] Abortar y arreglar primero
  [3] Mostrar detalles específicos de un error
```

---

## Flujo de Uso (Conversacional)

### Escenario 1: Modificaste `docker-compose.yml`

**Tú:**
> "Modifiqué el docker-compose para agregar Redis, commit y push"

**Yo:**
1. Detecto cambios en `docker-compose.yml`
2. Valido contra reglas Coolify
3. Genero mensaje: `feat(docker-compose): agregar Redis`
4. Hago commit y push
5. Espero a Coolify
6. Reporto: "✅ Despliegue iniciado en Coolify. Estado: Building (2/5). Logs disponibles en panel."

---

### Escenario 2: Cambios en código + config

**Tú:**
> "Actualicé API y agregué nueva variable de entorno. Commitea todo."

**Yo:**
1. Detecta cambios en `src/` y `.env.example`
2. Valida:
   - ✅ No hay secretos hardcodeados
   - ✅ Nueva variable documentada
   - ✅ `docker-compose.yml` válido
3. Genera mensaje: `feat(api): actualizar endpoints + agregar VAR_NUEVA`
4. Commit y push
5. Coolify deploya
6. Confirmo: "✅ Despliegue completado. Servicio running. Health: ✅ healthy."

---

### Escenario 3: Fix rápido de bug

**Tú:**
> "Corregí el healthcheck que fallaba. Commit y push."

**Yo:**
1. Detecta cambio en `docker-compose.yml` (healthcheck)
2. Valida que el fix es correcto
3. Genera: `fix: corregir healthcheck PostgreSQL start_period`
4. Commit y push
5. Coolify redeploya
6. Verifica: "✅ Healthcheck ahora pasa. DB status: healthy."

---

## Comandos Explícitos (Si prefieres Control Total)

Si no quieres que genere automáticamente, puedes ser explícito:

> "Commit: 'feat(docker): agregar Redis' y push a main"

Entonces yo:
1. Valido cambios
2. Hago commit exactamente con ese mensaje
3. Push a main
4. Creo tag si es main
5. Espero Coolify

---

## Casos de Excepción y Manejo

### Excepción 1: `.env` fue modificado

**Detección:**
```bash
git diff --name-only | grep -E "^\.env$|^\.env\."
```

**Acción:**
```
❌ DETENER - .env detectado en cambios

El archivo .env NO debe ser versionado (contiene secretos).

Acciones:
1. Ejecutar: git reset .env
2. Asegurar que .env está en .gitignore
3. Rehacer cambios en .env.example (sin valores reales)
4. Retry commit

¿Deseas que lo arregle automáticamente? (s/n)
```

---

### Excepción 2: Conflicto con rama remota

**Detección:**
```bash
git push origin $BRANCH 2>&1 | grep -i "rejected\|conflict"
```

**Acción:**
```
⚠️ CONFLICTO DE RAMA

Push fue rechazado. Alguien más hizo push recientemente.

Acciones:
1. Ejecutar: git pull origin $BRANCH
2. Resolver conflictos si hay
3. Rehacer commit
4. Retry push

¿Deseas que lo haga automáticamente? (s/n)
```

---

### Excepción 3: Coolify no detecta cambio (webhook falla)

**Detección (después de 30s):**
```bash
# Si después de 30s no hay nuevo deployment en Coolify
if [ $(date +%s) - $PUSH_TIME -gt 30 ]; then
  if ! ssh root@217.216.81.73 "docker ps" | grep -q "recently started"; then
    echo "❌ Webhook no llegó a Coolify"
  fi
fi
```

**Acción:**
```
⚠️ WEBHOOK TARDÍO

Coolify no ha detectado el cambio después de 30s.

Opciones:
1. Esperar más (a veces tarda hasta 2 minutos)
2. Verificar manualmente en Coolify UI
3. Revisar logs de GitHub Actions (si están configurados)
4. Forzar redeploy manual en Coolify UI

¿Qué prefieres?
```

---

## Variables de Entorno (Configuración Necesaria)

Para que este skill funcione sin intervención, necesita:

```bash
# Git
GIT_USER_NAME="Tu Nombre"
GIT_USER_EMAIL="tu@email.com"

# GitHub (para reportes si está en CI)
GITHUB_TOKEN=ghp_xxxxxxxxxxxx

# Coolify (para monitoreo de deploys)
COOLIFY_API_URL=http://217.216.81.73:8000
COOLIFY_API_TOKEN=xxxxx (si está disponible)

# VPS
VPS_IP=217.216.81.73
VPS_USER=root
VPS_SSH_KEY=/path/to/key
```

**Si algo falta:**
```
⚠️ Configuración incompleta

Necesito:
- GIT_USER_NAME: "Tu Nombre"
- GIT_USER_EMAIL: "tu@email.com"

Sin esto, no puedo hacer commits.

Configura primero:
  git config --global user.name "Tu Nombre"
  git config --global user.email "tu@email.com"
```

---

## Referencia Rápida: Comandos por Pasos

| Paso | Comando | Validación |
|------|---------|-----------|
| 1 | `git status` | Hay cambios? |
| 2 | `docker-compose config` | YAML válido? |
| 2 | `grep -i "latest" docker-compose.yml` | Versiones fijas? |
| 2 | `git diff \| grep -i "password"` | No credenciales? |
| 3 | `git commit -m "tipo: descripción"` | Mensaje estructurado? |
| 4 | `git add <archivos>` | Archivos correctos? |
| 5 | `git push origin $BRANCH` | Push exitoso? |
| 5 | `git tag -a vX.Y.Z` | Tag creado si main? |
| 6 | SSH a Coolify | Webhook llegó? |
| 7 | `docker ps` en Coolify | Container running? |
| 7 | `docker logs <container>` | Logs limpios? |

---

## Integración Final

Este skill **cierra el ciclo completo**:

```
Archivos modificados localmente
         ↓
validate-coolify-* (validaciones)
         ↓
git commit + push automático ← (ESTE SKILL)
         ↓
Webhook → Coolify
         ↓
Despliegue automático
         ↓
✅ En producción
```

---

## Notas Importantes

- **Nunca fuerza push**: Usa solo `git push`, nunca `git push -f`
- **Siempre valida antes**: Los 8 checkpoints son obligatorios
- **Cancela si hay secretos**: Si detecta `.env` o credenciales, DETÉN todo
- **Confirmación del usuario**: Pide OK antes de pasos críticos (push, tag)
- **Monitoreo post-deploy**: Espera confirmación de que Coolify desplegó correctamente antes de dar OK final

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chris78rey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
