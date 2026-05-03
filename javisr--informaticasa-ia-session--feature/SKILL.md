---
name: feature
description: Diseña y documenta una nueva feature. Analiza la idea, hace preguntas, identifica edge cases y puntos de seguridad. Usa /feature <idea> para iniciar el proceso de diseno. Use when this capability is needed.
metadata:
  author: javisr
---

# Skill: Feature Designer

Ayuda a diseñar y documentar nuevas features de forma exhaustiva.

## Uso

El usuario invocara este skill con `/feature <descripcion de la idea>`.

## Instrucciones

### Paso 1: Contexto Inicial

Antes de empezar, recopila informacion del proyecto:

1. Lee `docs/features/CHANGELOG.md` para ver features existentes
2. Usa `application-info` para conocer el stack
3. Usa `database-schema` para entender el modelo de datos
4. Revisa `docs/features/` para ver el formato de documentacion usado

### Paso 2: Desarrollar la Idea

Con la idea proporcionada:

1. **Resume** la feature en 2-3 oraciones claras
2. **Identifica actores**: usuario anonimo, autenticado, admin, sistema, cron, etc.
3. **Lista acciones principales** que cada actor podra realizar

### Paso 3: Hacer Preguntas (OBLIGATORIO)

NUNCA procedas sin hacer preguntas. Pregunta sobre:

**Alcance:**
- "Solo para usuarios autenticados o tambien anonimos?"
- "Aplica a todos los usuarios o hay roles especificos?"
- "Es una feature de pago o gratuita?"

**Limites:**
- "Hay limite de cantidad/tamano/frecuencia?"
- "Que pasa si se excede el limite?"

**Interacciones:**
- "Como interactua con [feature existente X]?"
- "Debe enviar notificaciones? A quien?"

**Datos:**
- "Que datos son publicos vs privados?"
- "Hay datos sensibles involucrados?"

### Paso 4: Identificar Edge Cases

Piensa en casos raros y problematicos:

**Concurrencia:**
- Dos usuarios editando lo mismo
- Doble click en botones
- Race conditions

**Datos extremos:**
- Datos vacios, null, muy largos
- Caracteres especiales, emojis, unicode
- Datos duplicados

**Estados inesperados:**
- Sesion expirada a mitad de proceso
- Error de red durante operacion
- Usuario eliminado/desactivado durante uso

**Tiempo:**
- Zonas horarias
- Datos historicos vs nuevos
- Expiracion de tokens/links

### Paso 5: Criterios de Aceptacion

Define criterios claros y verificables:

**Formato DADO/CUANDO/ENTONCES:**
```
- [ ] DADO [contexto], CUANDO [accion], ENTONCES [resultado esperado]
```

**Tipos de criterios:**
- **Funcionales**: Comportamientos que DEBEN funcionar
- **Calidad**: Tests necesarios, cobertura, performance
- **UX**: Mensajes de error, estados de carga, accesibilidad

Ejemplo:
- [ ] DADO un usuario autenticado, CUANDO sube una imagen > 5MB, ENTONCES ve mensaje "Imagen muy grande, maximo 5MB"
- [ ] DADO un admin, CUANDO elimina un usuario, ENTONCES el usuario se desactiva (soft delete)

### Paso 6: Puntos de Seguridad (CRITICO)

SIEMPRE identifica:

**IDOR (Insecure Direct Object Reference):**
- Usuario A NO debe ver/modificar datos de usuario B
- Validar ownership en CADA endpoint
- IDs no deben ser predecibles (usar UUIDs si es necesario)

**Autorizacion:**
- Verificar permisos en cada accion
- Prevenir manipulacion de requests
- Rate limiting para prevenir abusos

**Validacion:**
- SIEMPRE validar en backend (nunca confiar en frontend)
- Sanitizar inputs para prevenir XSS/SQL injection
- Validar uploads (tipo, tamano, contenido)

**Exposicion de datos:**
- No exponer datos sensibles en responses
- No loguear datos sensibles
- Encriptar datos que lo requieran

### Paso 7: Definir Alcance

Crea listas explicitas:

**IN SCOPE:**
- Funcionalidades que SI entran en esta version
- Casos de uso que SI se manejan

**OUT OF SCOPE:**
- Funcionalidades para versiones futuras
- Casos que NO se manejaran (y por que)

### Paso 8: Generar Documento

Si el usuario esta satisfecho con el analisis, genera el documento en:
`docs/features/[nombre-feature].plan.md`

Usa el formato de documentos existentes en `docs/features/` como referencia.

El documento debe incluir:
- Resumen
- Actores y permisos
- Alcance (IN/OUT)
- Flujos principales
- Edge cases con comportamiento esperado
- Criterios de aceptacion (funcionales, calidad, UX)
- Checklist de seguridad
- Modelo de datos (si aplica)
- Preguntas pendientes (si quedan)

## Reglas Importantes

1. **NUNCA saltes las preguntas** - Es mejor preguntar de mas que de menos
2. **Piensa como atacante** - Que harias para abusar de esta feature?
3. **Se especifico** - "Validar permisos" no basta, di DONDE y COMO
4. **Busca documentacion** - Usa `search-docs` antes de proponer soluciones tecnicas
5. **Revisa el schema** - Entiende las relaciones antes de proponer cambios

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/javisr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
