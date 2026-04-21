---
name: experto-en-maquetacin-avanzada-vanguard-layout
description: Protocolo estratégico para maquetación web de alto nivel (Premium/Wowsome). Enfoque en CSS moderno, arquitectura escalable y estética superior. Use when this capability is needed.
metadata:
  author: davidfunes
---

# 🏗️ Protocolo: Experto en Maquetación Avanzada

Este protocolo define los estándares para la creación de interfaces web en el ecosistema "David Funes Music", asegurando un nivel de acabado **Junior Senior+ / COO** (Premium).

## 🧠 Mentalidad del "Dream Team"
- **UX Lead**: No construimos cajas; creamos atmósferas. La ergonomía y el ritmo visual son innegociables.
- **Architect**: El código debe ser una prosa estructurada. Priorizamos propiedades lógicas y escalabilidad (SOLID).

## 🛠️ Tecnologías de Vanguardia

### 1. Layout Master System
- **CSS Grid (Moderno)**: Uso obligatorio de `grid-template-areas` para legibilidad.
    - *Expert Tip*: Aprovechar `subgrid` para heredar estructuras de padres a hijos sin romper la alineación.
    - *Auto-Layout*: Uso inteligente de `minmax(0, 1fr)` para evitar desbordamientos.
- **Container Queries**: Diseñar componentes que se adaptan a su contenedor, no solo al viewport. Uso de la unidad `cqw` y `@container`.

### 2. Tipografía y Espaciado Fluido
- **Protocolo Clamp**: Prohibido usar tamaños estáticos. Todo debe ser proporcional.
    - `font-size: clamp(min, preferred, max)`
    - `padding: clamp(min, preferred, max)`
- **Unidades Dinámicas**: Uso de `dvh`, `svh` y `lvh` para evitar problemas con las barras de navegación en móviles.

### 3. Selectores de Próxima Generación
- **Selector `:has()`**: El "selector de padre". Utilizar para cambios de estado complejos sin necesidad de JavaScript pesado.
- **Selectores `:is()` y `:where()`**: Reducir la especificidad y mantener el CSS limpio (estilo Arquitecto).

### 4. Propiedades Lógicas (Internalización)
- Uso de `margin-inline`, `padding-block`, `inset-inline-start` para asegurar que el diseño sea agnóstico a la dirección del texto por diseño.

## 🎨 Estética Superior (Wowsome)
- **Glassmorphism**: Uso de `backdrop-filter: blur()` combinado con bordes semi-transparentes y gradientes sutiles.
- **Micro-interacciones**: Transiciones suaves con `bezier-curves` personalizadas (`ease-in-out` estándar es insuficiente).
- **Depth & Layers**: Uso de `z-index` semántico y sombras multidimensionales (`box-shadow` apilados).

## 🛡️ Reglas de Oro de Maquetación
1. **No Placeholders**: Si falta una imagen, se genera una premium con `generate_image`.
2. **Performance First**: Minimizar el uso de librerías externas. Si CSS puede hacerlo, CSS lo hará.
3. **Responsive is Default**: No es un extra; se diseña "Mobile First" pero se optimiza para pantallas "Ultra-Wide".
4. **Clean DOM**: Mantener un árbol de nodos ligero. Evitar el "Divitis".

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davidfunes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
