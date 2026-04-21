---
name: deploy-manager
description: Skill para gestionar el proceso de deployment, incluyendo validación pre-deploy, ejecución y verificación post-deploy. Use when this capability is needed.
metadata:
  author: julianbenavidesdvt
---

# Gestor de Deployment

Esta skill centraliza las operaciones de deployment, asegurando que todos los pasos de validación se ejecuten antes del deploy y que el sistema se verifique después.

## Instrucciones

### 1. Validación Pre-Deploy (Pre-Deploy Checklist)

Utiliza esta función antes de cualquier deployment.

1. **Verificar Estado del Código**:
   ```bash
   # Verificar rama correcta
   git branch --show-current

   # Verificar no hay cambios sin commit
   git status --porcelain

   # Verificar sincronización con remote
   git fetch origin && git status -uno
   ```

2. **Ejecutar Validaciones**:

   | Validación | Comando | Criterio de Éxito |
   |------------|---------|-------------------|
   | Linting | `npm run lint` | 0 errores |
   | Type Check | `npm run type-check` / `tsc --noEmit` | 0 errores |
   | Unit Tests | `npm test` | 100% pasando |
   | Integration Tests | `npm run test:integration` | 100% pasando |
   | Build | `npm run build` | Exitoso |
   | Security Audit | `npm audit --audit-level=high` | 0 vulnerabilidades altas |

3. **Verificar Artefactos**:
   - [ ] `CHANGELOG.md` actualizado (si aplica)
   - [ ] Versión incrementada en `package.json`
   - [ ] Variables de entorno documentadas
   - [ ] Migraciones de DB listas

4. **Generar Reporte Pre-Deploy**:

   ```markdown
   # Pre-Deploy Checklist: [Environment]

   ## Estado del Código
   - Rama: [branch-name]
   - Commit: [sha]
   - Fecha: [timestamp]

   ## Validaciones
   | Check | Estado |
   |-------|--------|
   | Lint | ✅/❌ |
   | Types | ✅/❌ |
   | Tests | ✅/❌ |
   | Build | ✅/❌ |
   | Security | ✅/❌ |

   ## Decisión
   - **READY**: Todas las validaciones pasan
   - **BLOCKED**: [Lista de issues]
   ```

### 2. Ejecutar Deployment (Execute Deploy)

Utiliza esta función para ejecutar el deployment según el entorno.

1. **Identificar Entorno y Estrategia**:

   | Entorno | Estrategia | Aprobación |
   |---------|------------|------------|
   | Development | Direct push | Automática |
   | Staging | CI/CD pipeline | Automática |
   | Production | Blue-Green / Canary | Manual |

2. **Deploy por Plataforma**:

   **Google Cloud Run**:
   ```bash
   # Build y push imagen
   gcloud builds submit --tag gcr.io/[PROJECT]/[SERVICE]

   # Deploy
   gcloud run deploy [SERVICE] \
     --image gcr.io/[PROJECT]/[SERVICE] \
     --region [REGION] \
     --platform managed
   ```

   **Kubernetes (GKE)**:
   ```bash
   # Aplicar configuración
   kubectl apply -f k8s/

   # Verificar rollout
   kubectl rollout status deployment/[DEPLOYMENT]
   ```

   **Terraform (Infrastructure)**:
   ```bash
   # Plan
   terraform plan -out=tfplan

   # Apply (con aprobación)
   terraform apply tfplan
   ```

3. **Registrar Deployment**:
   - Commit SHA deployado
   - Timestamp de deploy
   - Usuario que ejecutó
   - Ambiente destino

### 3. Verificación Post-Deploy (Post-Deploy Verification)

Utiliza esta función inmediatamente después del deployment.

1. **Health Checks**:
   ```bash
   # Verificar endpoint de salud
   curl -f https://[SERVICE_URL]/health

   # Verificar versión deployada
   curl https://[SERVICE_URL]/version
   ```

2. **Smoke Tests**:

   | Test | Endpoint | Expected |
   |------|----------|----------|
   | Health | `/health` | 200 OK |
   | API disponible | `/api/v1/status` | 200 + body |
   | Auth funciona | `/api/v1/auth/verify` | 200/401 |
   | DB conectada | `/health/db` | 200 OK |

3. **Monitoreo Inicial**:
   - Verificar logs por errores (primeros 5 minutos)
   - Verificar métricas de latencia
   - Verificar tasa de errores

4. **Criterios de Rollback**:

   | Señal | Threshold | Acción |
   |-------|-----------|--------|
   | Error rate | > 5% | Rollback inmediato |
   | Latencia P99 | > 2x baseline | Investigar |
   | Health check falla | 3 consecutivos | Rollback |

### 4. Rollback (Emergency Rollback)

Utiliza esta función si el deploy falla verificación.

1. **Identificar Versión Anterior**:
   ```bash
   # Ver historial de deploys (Cloud Run)
   gcloud run revisions list --service=[SERVICE]

   # Ver historial (Kubernetes)
   kubectl rollout history deployment/[DEPLOYMENT]
   ```

2. **Ejecutar Rollback**:
   ```bash
   # Cloud Run - traffic a revisión anterior
   gcloud run services update-traffic [SERVICE] \
     --to-revisions=[PREVIOUS_REVISION]=100

   # Kubernetes
   kubectl rollout undo deployment/[DEPLOYMENT]
   ```

3. **Documentar Incidente**:
   ```markdown
   # Rollback Report

   ## Información
   - Fecha: [timestamp]
   - Ambiente: [env]
   - Versión fallida: [version]
   - Versión rollback: [version]

   ## Razón del Rollback
   [Descripción del problema]

   ## Impacto
   - Duración: [minutos]
   - Usuarios afectados: [estimado]

   ## Próximos Pasos
   - [ ] Investigar causa raíz
   - [ ] Crear fix
   - [ ] Re-deploy con fix
   ```

### 5. Generar Release Notes (Generate Release Notes)

Utiliza esta función para documentar releases.

1. **Obtener Cambios**:
   ```bash
   # Commits desde último release
   git log v[PREVIOUS]..HEAD --oneline

   # PRs mergeados
   gh pr list --state merged --base main --limit 50
   ```

2. **Categorizar Cambios**:
   - **Features**: Nuevas funcionalidades
   - **Fixes**: Corrección de bugs
   - **Performance**: Mejoras de rendimiento
   - **Security**: Parches de seguridad
   - **Breaking**: Cambios que rompen compatibilidad

3. **Generar Documento**:
   ```markdown
   # Release Notes v[VERSION]

   **Fecha**: [YYYY-MM-DD]
   **Ambiente**: Production

   ## Highlights
   - [Feature principal]

   ## Features
   - [FEAT-001] Descripción (#PR)

   ## Bug Fixes
   - [FIX-001] Descripción (#PR)

   ## Breaking Changes
   - [BREAK-001] Descripción + migración

   ## Upgrade Guide
   1. Paso para actualizar

   ## Known Issues
   - [Issue link]
   ```

## Integración con Flujo

```
/implement → /test → code_review → APPROVE
                                      ↓
                            deploy_manager: Pre-Deploy
                                      ↓
                            (Validaciones pasan)
                                      ↓
                            deploy_manager: Execute
                                      ↓
                            deploy_manager: Post-Deploy
                                      ↓
                            ✅ Success / ❌ Rollback
```

## Ambientes Soportados

| Ambiente | URL Pattern | Auto-Deploy |
|----------|-------------|-------------|
| Dev | dev.[domain] | Sí (push to dev) |
| Staging | staging.[domain] | Sí (merge to main) |
| Production | [domain] | No (manual trigger) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/julianbenavidesdvt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
