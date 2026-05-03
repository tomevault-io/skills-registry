---
name: pre-deployment-security-check
description: Verifica que no haya credenciales, secrets o archivos sensibles antes de pushear a GitHub. Busca API keys hardcodeadas, .env con valores reales, y valida .gitignore. Use when this capability is needed.
metadata:
  author: alannreyes
---

# 🔒 Pre-Deployment Security Check

Realiza validaciones críticas de seguridad antes de hacer push a GitHub. Evita la fuga de credenciales.

## Proceso de Validación

### 1️⃣ Buscar Variables de Entorno Sensibles en Código

Busca en archivos de código (no en .env, config files):
- `GEMINI_API_KEY`
- `JWT_SECRET`
- `DATABASE_PASSWORD`
- `AZURE_AD_CLIENT_SECRET`
- Credenciales hardcodeadas en archivos `.ts`, `.js`, `.tsx`, `.jsx`

```bash
# Comandos a ejecutar:
grep -r "GEMINI_API_KEY\s*=" backend/src --include="*.ts" --include="*.js"
grep -r "JWT_SECRET\s*=" backend/src --include="*.ts"
grep -r "DATABASE_PASSWORD\s*=" backend/src --include="*.ts"
grep -r "password:\s*['\"]" backend/src --include="*.ts"
grep -r "secret:\s*['\"]" backend/src --include="*.ts"
```

### 2️⃣ Verificar que .env NO esté tracked en Git

```bash
git status | grep ".env" # No debe aparecer
git ls-files | grep ".env" # No debe aparecer
```

### 3️⃣ Validar Contenido de .env.example

El archivo `.env.example` debe tener valores de PLACEHOLDER, no reales:
```bash
grep -E "your_|placeholder|example|dev_key" backend/.env.example
```

### 4️⃣ Revisar .gitignore

Validar que incluya:
- `.env*` (excepto .env.example)
- `dist/`
- `node_modules/`
- `.env.production`
- `*.key`
- `*.pem`

### 5️⃣ Buscar Secretos Comunes

Usar patrones regex para detectar:
- API keys de formato conocido
- Tokens de Bearer
- Conexiones de base de datos
- URLs con credenciales (user:pass@host)

```bash
grep -r "mongodb://.*:.*@" backend/src --include="*.ts"
grep -r "postgres://.*:.*@" backend/src --include="*.ts"
grep -r "Bearer\s*[A-Za-z0-9\-_.]" backend/src --include="*.ts"
```

### 6️⃣ Verificar Archivos de Configuración

Revisar archivos que podrían contener secrets:
- `.env` (debe estar en .gitignore)
- `.env.production` (debe estar en .gitignore)
- `secrets.json` (debe estar en .gitignore)
- `credentials.json` (debe estar en .gitignore)

### 7️⃣ Listar Cambios a Commitar

```bash
git status --short
git diff --cached --name-only
```

## Salida Esperada

```
✅ SECURITY CHECK REPORT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

✓ No hardcoded API keys encontradas
✓ No hardcoded passwords encontradas
✓ .env NO está en tracking
✓ .env.production NO está en tracking
✓ .gitignore está correctamente configurado
✓ No URLs con credenciales encontradas

📋 Cambios a hacer push:
  M backend/src/services/sync.service.ts
  M frontend/app/page.tsx
  A backend/src/new-module.ts

✅ SEGURO PARA PUSHEAR
```

O en caso de problemas:

```
🔴 SECURITY CHECK FAILED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

❌ CRÍTICO: Encontradas credenciales hardcodeadas
   Archivo: backend/src/config/database.ts:15
   Contenido: GEMINI_API_KEY = "AIzaSy..."

❌ .env está en tracking (debe estar en .gitignore)

❌ Archivo detectado: secrets.json (debe estar en .gitignore)

🔧 ACCIONES RECOMENDADAS:
1. Elimina las credenciales de database.ts
2. Usa process.env.GEMINI_API_KEY en su lugar
3. Añade .env a .gitignore
4. Ejecuta: git rm --cached .env
5. Ejecuta: git rm --cached secrets.json
6. Vuelve a ejecutar este check
```

## Uso

Invoca este skill cuando estés listo para hacer push:

```
"Push al repo, pero primero hazme un security check"
"Quiero hacer push, verifica que no haya credenciales expuestas"
"Pre-deploy security check por favor"
```

Claude automáticamente ejecutará las validaciones y te reportará si es seguro proceder.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alannreyes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
