---
name: catalog-buyer
description: Optimiza la compra de catálogo de discos de vinilo para Vinilos Cusco. Usa este skill cuando el usuario necesite (1) Analizar catálogos de proveedores y seleccionar discos para compra dentro de un presupuesto, (2) Calcular PVP óptimo para discos considerando rotación y mercado, (3) Evaluar propuestas de compra con análisis de márgenes, (4) Comparar discos del catálogo actual vs proveedores, o (5) Generar tablas de compra con recomendaciones estratégicas. El skill maneja múltiples monedas, costos de envío, descuentos de proveedores y búsqueda de precios de mercado. Use when this capability is needed.
metadata:
  author: jorgejhms
---

# Catalog Buyer - Vinilos Cusco

Este skill optimiza el proceso de compra de catálogo de discos de vinilo, ayudando a tomar decisiones informadas sobre qué comprar, en qué cantidad, y a qué precio vender.

## Escenarios de Uso

El skill maneja tres escenarios principales. **En TODOS los casos se hace matching con el inventario actual para prevenir duplicados.**

### 1. Compra Completa (Catálogo Actual + Catálogo Proveedor)
Usuario proporciona ambos catálogos y presupuesto → Skill selecciona discos óptimos para comprar, priorizando discos nuevos.

### 2. Cálculo de PVP (Lista de Discos con Precio de Costo)
Usuario proporciona tabla con discos y precios de costo → **Skill verifica contra inventario actual** → Alerta sobre duplicados → Calcula PVP óptimo.

### 3. Evaluación de PVP (Lista de Discos con PVP Propuesto)
Usuario proporciona tabla con discos y PVP propuestos → **Skill verifica contra inventario actual** → Alerta sobre duplicados → Evalúa si los PVP son óptimos.

## Workflow Principal

### Fase 1: Análisis de Contexto

1. **Identificar escenario** (Compra Completa / Cálculo PVP / Evaluación PVP)
2. **Verificar disponibilidad de catálogo actual**:
   - Si el usuario NO proporciona su inventario actual → **Preguntar explícitamente por él**
   - El matching con inventario es OBLIGATORIO en todos los escenarios
   - Sin catálogo actual, no se puede prevenir duplicados
3. **Extraer información clave**:
   - Presupuesto disponible (si aplica)
   - Origen del proveedor (nacional/internacional)
   - Moneda de los precios
   - Costos de envío
   - Descuentos aplicables
   - Prioridades especiales del usuario
   - **Formato del producto**: Por defecto SOLO vinilos. Incluir CDs solo si el usuario lo menciona explícitamente

4. **Procesar archivos**:
   - Si es Excel: usar pandas para análisis
   - Si es PDF: extraer tablas con pdfplumber o similar
   - Normalizar columnas: identificar artista, álbum, precio, cantidad, etc.
   - **IMPORTANTE**: Filtrar y excluir cualquier producto que sea CD, a menos que el usuario haya indicado explícitamente que quiere CDs. Buscar indicadores como "CD", "Compact Disc" en el nombre del producto o columnas de formato

### Fase 2: Matching de Discos (OBLIGATORIO en TODOS los escenarios)

**CRÍTICO**: Siempre comparar con el catálogo actual, incluso cuando el usuario proporciona una tabla de discos específicos. Esto previene compras duplicadas.

Comparar catálogo proveedor/tabla de discos con catálogo actual:

```python
# Lógica de matching FUZZY:
# 1. Normalizar nombres (lowercase, quitar acentos, quitar espacios extras)
# 2. Remover caracteres especiales y paréntesis para matching base
# 3. Buscar coincidencias por: "artista + álbum" (fuzzy match)
# 
# Ejemplos de matches válidos (mismo disco):
# - "Libido - Hembra" == "Libido - Hembra (Verde)" ✓
# - "Pink Floyd - The Wall" == "Pink Floyd - The Wall [2LP]" ✓
# - "The Beatles - Abbey Road" == "Beatles - Abbey Road" ✓
#
# Considerar coincidencia si:
# - Artista coincide (fuzzy) Y álbum coincide al 85%+
# - Ignorar texto entre paréntesis/corchetes para matching base
```

**Proceso de matching**:

1. **Cargar catálogo actual**: Si el usuario no lo proporciona, preguntar explícitamente
2. **Para cada disco en la propuesta/tabla**:
   - Hacer fuzzy match con inventario actual
   - Si hay match → Marcar como "YA EN INVENTARIO" 
   - Si NO hay match → Candidato válido para compra
3. **Identificar variantes**: 
   - "Libido - Hembra" (tenemos) vs "Libido - Hembra (Verde)" (proveedor)
   - Señalar como variante, usuario decide si quiere ambas
4. **Output del matching**:
   - En tabla final, incluir columna "Estado Inventario"
   - Valores: "Nuevo", "Ya tenemos", "Variante (tenemos versión X)"

**IMPORTANTE en Escenarios 2 y 3**: 
- Aunque el usuario proporcione tabla específica de discos
- SIEMPRE verificar contra inventario actual
- Alertar sobre duplicados antes de calcular PVP
- Preguntar si igual quiere el duplicado (ej: para tener stock)

### Fase 3: Clasificación de Rotación

Para cada disco candidato, determinar su rotación (A/B/C) usando búsqueda web. **La rotación refleja DEMANDA, no popularidad general**.

- **Rotación A (Alta Demanda)**: Discos que se venderán rápido
  - Álbumes clásicos icónicos con demanda constante (The Beatles - Abbey Road)
  - Best sellers actuales o trending
  - "Joyas raras" con alta demanda entre coleccionistas (ediciones limitadas buscadas)
  - Indicadores: "best-selling", "highly sought after", "in-demand", "collector's item"
  
- **Rotación B (Demanda Media)**: Discos con ventas predecibles pero no urgentes
  - Álbumes de artistas reconocidos, obras secundarias
  - Catálogo sólido que se vende consistentemente
  - Indicadores: "popular album", "well-received", "steady sales"
  
- **Rotación C (Demanda Baja/Incierta)**: Discos de nicho que pueden tardar en venderse
  - Artistas poco conocidos por público general
  - Bandas de culto con audiencia pequeña pero leal
  - Discos que aportan "prestigio" o exclusividad a la tienda
  - Pueden ser importantes para la identidad de la tienda pero rotación lenta
  - Indicadores: "indie artist", "cult following", "niche", "underground"

**Proceso**:
1. Buscar: "[artista] [álbum] vinyl demand sought after collector"
2. Analizar popularidad general + demanda específica de coleccionistas
3. Un disco puede ser desconocido pero muy demandado (A) o famoso pero lenta rotación (B)
4. Considerar el contexto de Cusco y los géneros de mayor demanda local

### Fase 4: Investigación de Mercado y Contexto del Álbum

Para cada disco candidato, recopilar información completa:

**A) Contexto del álbum** (para tabla de output):
1. Buscar: "[artista] [álbum] album review description"
2. Extraer:
   - **Descripción breve** (1-2 líneas): Síntesis del álbum, significado, importancia
   - **Géneros musicales**: Clasificación de género(s) del álbum
3. Fuentes confiables: AllMusic, Discogs, Wikipedia, Rolling Stone, Pitchfork

**B) Precios de referencia** (para calcular PVP óptimo):
1. **BuscaVinilos.pe**: Metabuscador principal
   - Buscar: "[artista] [álbum] vinyl peru"
   - Extraer rango de precios de múltiples tiendas
   
2. **Otras tiendas online peruanas** (si es necesario):
   - Buscar: "[artista] [álbum] vinyl site:pe"

3. **Análisis de competencia**:
   - Identificar precio mínimo, máximo y promedio del mercado
   - Excluir precios extremos (outliers)

### Fase 5: Cálculo de Costos y PVP

#### Costo Total por Disco

```
Costo Base = Precio del proveedor
+ Descuento aplicable (si existe)
+ Tipo de cambio (si aplica, usar script get_exchange_rate.py)
+ Prorrateo de envío (costo envío / cantidad de discos)

Costo Total = Costo Base + Envío Prorrateado
```

#### PVP Óptimo

Usar estrategia híbrida considerando:

1. **Margen objetivo base**:
   - Proveedor nacional: 50%
   - Proveedor internacional: 40%

2. **Ajuste por rotación (estrategia de demanda)**:
   - **Rotación A** (alta demanda): **MAXIMIZAR margen** (50-70%)
     - Se venderán rápido, la demanda lo permite
     - Aprovechar la rotación rápida para mayor ganancia
     - Ejemplo: Beatles - Abbey Road puede tener margen alto porque se venderá igual
   
   - **Rotación B** (demanda media): **Mantener margen objetivo** (40-50%)
     - Balance entre competitividad y ganancia
     - Ventas predecibles con margen estándar
   
   - **Rotación C** (demanda baja): **REDUCIR margen** (20-35%)
     - Hacer más atractivo el precio para compensar baja demanda
     - Preferible vender con margen bajo que no vender
     - Genera prestigio para la tienda pero necesita ser accesible
     - Ejemplo: Banda indie poco conocida necesita precio atractivo

3. **Ajuste por mercado**:
   ```
   Si PVP calculado > Precio máximo mercado:
     → Ajustar hacia abajo para ser competitivo
     → No exceder 10-15% sobre precio promedio mercado
   
   Si PVP calculado < Precio mínimo mercado:
     → PARA ROTACIÓN A/B: Subir precio aprovechando posicionamiento
     → PARA ROTACIÓN C: Mantener precio bajo para facilitar venta
     → Siempre mantener margen mínimo aceptable (20%)
   ```

4. **Redondeo**:
   - Redondear a números "psicológicos": X.90, X.50, o enteros
   - Ejemplo: 87.32 → 87.90 o 85.00

### Fase 6: Selección de Compra (Solo Escenario 1)

Optimizar selección dentro del presupuesto:

1. **Priorizar variedad**: Maximizar cantidad de títulos diferentes
2. **Incluir rotación A**: Asegurar algunos best sellers (rápida rotación)
3. **Balance de géneros**: Diversificar oferta
4. **Cantidad por título**: Generalmente 1 unidad, salvo que usuario especifique

**Algoritmo de selección**:
```python
# 1. Ordenar candidatos por "valor" (considerando rotación, margen, novedad)
# 2. Seleccionar iterativamente hasta agotar presupuesto
# 3. Priorizar discos con mejor relación calidad-precio-rotación
```

### Fase 7: Generación de Output

**IMPORTANTE**: El output final es un **informe de texto** con tablas en markdown. NO crear archivos Excel automáticamente a menos que el usuario lo solicite explícitamente después del informe.

Crear tabla con las siguientes columnas:

| Artista | Álbum | Descripción | Géneros | Estado Inv | Cant | Rot | Costo | PVP | Margen % | Precio Mercado | Notas |
|---------|-------|-------------|---------|------------|------|-----|-------|-----|----------|----------------|-------|

**Columnas**:
- **Artista**: Nombre del artista
- **Álbum**: Título del álbum
- **Descripción**: Breve descripción del álbum (1-2 líneas, buscar en internet)
  - Ejemplo: "Álbum debut icónico que definió el post-punk"
  - Ejemplo: "Obra conceptual sobre la alienación urbana"
- **Géneros**: Género(s) musical(es) del álbum
  - Ejemplo: "Rock Progresivo"
  - Ejemplo: "Jazz Fusion, Avant-Garde"
- **Estado Inv**: Estado en inventario actual
  - "Nuevo" - No lo tenemos
  - "Ya tenemos" - Mismo disco en inventario
  - "Variante" - Tenemos versión diferente (especificar en Notas)
- **Cant**: Cantidad a comprar (usualmente 1)
- **Rot**: Rotación A / B / C
- **Costo**: Costo total unitario (incluye envío prorrateado)
- **PVP**: Precio de venta público calculado
- **Margen %**: Porcentaje de ganancia
- **Precio Mercado**: Rango encontrado en mercado (ej: "S/. 85-110")
- **Notas**: Comentarios relevantes (oportunidad, alertas, etc.)

#### Resumen Ejecutivo

Incluir al inicio o final:

```
📊 RESUMEN DE COMPRA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Proveedor: [Nombre]
Inversión Total: S/. XXX.XX
Cantidad de Títulos: XX discos
Margen Promedio: XX%
Ganancia Proyectada: S/. XXX.XX
ROI Esperado: XX% (Ganancia / Inversión × 100)

Distribución por Rotación:
- Rotación A: X discos (best sellers)
- Rotación B: X discos (catálogo sólido)  
- Rotación C: X discos (nicho/especiales)

Distribución por Estado:
- Discos nuevos: X (amplían catálogo)
- Variantes: X (versiones diferentes de discos que ya tenemos)
- Duplicados evitados: X

💡 Presupuesto Restante: S/. XX.XX
```

#### Alertas y Oportunidades

Incluir sección de alertas:

```
🎯 OPORTUNIDADES DESTACADAS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
• [Artista - Álbum]: Rotación A con margen 65% - excelente oportunidad
• [Artista - Álbum]: Precio mercado S/. 120+, podemos ofrecer a S/. 95
• [Artista - Álbum]: Disco difícil de encontrar, alta rotación esperada

⚠️ CONSIDERACIONES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
• [Artista - Álbum]: Margen bajo (25%) pero alta rotación, recomendado
• [Artista - Álbum]: Verificar condición física antes de confirmar compra
```

## Manejo de Monedas y Tipo de Cambio

**Cuando los precios están en USD**:

1. Ejecutar script: `python3 scripts/get_exchange_rate.py`
2. Usar el tipo de cambio retornado (promedio mensual, redondeado hacia arriba)
3. Aplicar en todos los cálculos de costo

**Redondeo**: Siempre redondear hacia arriba a 1 decimal (nos beneficia).

Ejemplo:
```
Precio USD: $25.00
Tipo Cambio: 3.8 (redondeado de 3.75)
Costo PEN: S/. 95.00
```

## Manejo de Costos de Envío

### Proveedores Nacionales
- **Si hay envío gratis**: No incluir en costos
- **Si hay costo de envío**: Prorratear entre todos los discos
- **Promociones**: Aplicar según condiciones (ej: gratis con 10+ discos)

### Proveedores Internacionales
- **Usuario debe proporcionar**: Costo de envío en su moneda
- **Incluir en cálculo**: Convertir si es necesario y prorratear

Ejemplo:
```
Compra: 15 discos
Envío: $45.00
Envío por disco: $3.00 (añadir a cada costo unitario)
```

## Consideraciones Especiales

### Formato de Productos: Solo Vinilos por Defecto

**CRÍTICO**: Este skill está diseñado para compra de **vinilos únicamente**.

- **Por defecto**: Filtrar y excluir CDs, cassettes, o cualquier otro formato
- **Indicadores de exclusión**: "CD", "Compact Disc", formato digital, streaming
- **Excepción**: Incluir CDs SOLO si el usuario lo menciona explícitamente en su consulta
  - Ejemplo válido: "Busca tanto vinilos como CDs de rock clásico"
  - Sin mención explícita: Excluir todos los CDs

Al procesar catálogos, verificar columnas como "Formato", "Tipo", o buscar patrones en nombres de productos que indiquen formato.

### Descuentos de Proveedores

Los descuentos y condiciones de proveedores deben ser indicados explícitamente por el usuario en cada consulta, ya que varían con el tiempo. 

Cuando el usuario mencione descuentos:
- Aplicar el porcentaje exacto mencionado
- Confirmar si aplica a todos los productos o solo algunos
- Considerar si hay condiciones (ej: compra mínima)

### Variantes de Discos

Si un disco tiene variantes (color, gatefold, etc.):
- Mencionar en columna "Notas"
- Si la variante tiene premium de precio, indicarlo
- Considerar si la variante justifica mayor PVP
- Las variantes especiales pueden aumentar la rotación (más demanda de coleccionistas)

### Géneros y Contexto Local

Cusco tiene preferencias específicas que pueden influir en la rotación:
- **Rock clásico**: Alta demanda → Favorece rotación A/B
- **Rock latino**: Alta demanda → Favorece rotación A/B
- **Jazz/Blues**: Demanda media-alta → Generalmente rotación B
- **Electrónica**: Demanda media → Rotación B/C según artista
- **Música peruana**: Demanda variable → Evaluar caso por caso
- **Indie/Underground**: Puede ser rotación C pero aporta prestigio

## Referencias y Scripts

### Scripts Disponibles

- `scripts/get_exchange_rate.py`: Obtiene tipo de cambio USD→PEN promedio mensual

### Información de Proveedores

La información sobre proveedores (descuentos, condiciones de envío, etc.) debe ser proporcionada por el usuario en cada consulta o estar disponible en archivos del proyecto. Esta información varía con el tiempo y no se incluye en el skill.

El usuario debe indicar explícitamente:
- Descuentos aplicables del proveedor
- Condiciones de envío (costo, envío gratis según cantidad, etc.)
- Cualquier promoción especial vigente

## Validaciones y Errores Comunes

### Antes de generar output, verificar:

- [ ] **Matching con inventario realizado** (en TODOS los escenarios)
- [ ] **Descripción y géneros incluidos** para cada disco
- [ ] **SOLO vinilos incluidos** (CDs excluidos a menos que usuario lo pidiera)
- [ ] Todos los costos están en la misma moneda
- [ ] Se aplicaron los descuentos correctos
- [ ] El envío está prorrateado (si aplica)
- [ ] Los márgenes son realistas (mínimo 20%, objetivo 40-50%)
- [ ] Los PVP son competitivos según mercado
- [ ] La suma de costos no excede el presupuesto
- [ ] La rotación está fundamentada (búsqueda web realizada)
- [ ] **ROI calculado** en el resumen

### Errores comunes a evitar:

- ❌ No hacer matching con inventario actual (previene duplicados)
- ❌ Omitir descripción o géneros en la tabla
- ❌ Incluir CDs sin que el usuario lo pida explícitamente
- ❌ Crear archivos Excel automáticamente sin que se solicite
- ❌ No convertir monedas antes de calcular
- ❌ Olvidar prorratear el envío
- ❌ No buscar precios de mercado para PVP
- ❌ Proponer márgenes no competitivos
- ❌ No priorizar variedad sobre cantidad
- ❌ No considerar la rotación al calcular PVP

## Interacción con el Usuario

### Preguntar cuando sea necesario:

- Si el presupuesto no está claro
- Si faltan datos de envío en compras internacionales
- Si hay ambigüedad sobre descuentos aplicables
- Si el usuario quiere priorizar algo específico

### NO preguntar innecesariamente:

- Si la información está en los archivos proporcionados
- Si se puede inferir del contexto
- Si hay valores por defecto razonables

### Output Final:

- **Entregar informe de texto** con tablas markdown y análisis completo
- **NO crear archivos Excel/CSV automáticamente** al finalizar el análisis
- Si el usuario quiere un archivo después del informe, crearlo solo cuando lo solicite explícitamente

### Tono:

- Profesional pero accesible
- Enfocado en maximizar valor para el negocio
- Proactivo en identificar oportunidades
- Transparente sobre limitaciones o riesgos

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jorgejhms) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
