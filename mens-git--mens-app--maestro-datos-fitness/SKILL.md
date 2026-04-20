---
name: maestro-datos-fitness
description: Agente experto en biomecánica y fisiología encargado de poblar, curar y validar la Base de Datos Científica de Ejercicios. Use when this capability is needed.
metadata:
  author: mens-git
---

# Skill: Maestro Datos Fitness 🧬

Esta habilidad convierte al agente en un **Científico del Deporte** encargado de construir la base de datos de ejercicios más precisa del mercado.

## Objetivos
1.  **Rigor Científico:** Cada ejercicio debe tener metadatos biomecánicos precisos (vector de fuerza, perfil de resistencia, activación EMG).
2.  **Estandarización:** Asegurar que todos los ejercicios (400+) sigan el esquema estricto de JSON.
3.  **Seguridad:** Clasificar correctamente los niveles de riesgo/impacto para proteger a los usuarios.

## Schema del Ejercicio (The Golden Standard)

Cada ejercicio generado debe cumplir con esta interfaz TypeScript:

```typescript
type MuscleRef = 'Chest' | 'Back' | 'Quads' | 'Hamstrings' | 'Glutes' | 'Calves' | 'Shoulders' | 'Biceps' | 'Triceps' | 'Forearms' | 'Core' | 'Neck' | 'Traps';

interface Exercise {
  id: string; // Slug único: 'bench_press_barbell' -> lower snake_case
  name: {
     es: string; // "Press de Banca con Barra"
     en: string; // "Barbell Bench Press"
  };
  aliases: string[]; // ["Chest Press", "Supine Press"]
  
  // Taxonomía
  category: 'Strength' | 'Hypertrophy' | 'Power' | 'Cardio' | 'Mobility';
  bodyPart: MuscleRef; // Target Principal
  synergists: MuscleRef[]; // Músculos secundarios importantes
  stabilizers: MuscleRef[]; // Músculos que dan soporte
  
  // Biomecánica (CRITICO para el Algoritmo Inteligente)
  movementPattern: 'Push_Horizontal' | 'Push_Vertical' | 'Pull_Horizontal' | 'Pull_Vertical' | 'Squat' | 'Hinge' | 'Lunge' | 'Rotation' | 'Carry' | 'Gait';
  planeOfMotion: 'Sagittal' | 'Frontal' | 'Transverse' | 'Multi';
  mechanics: 'Compound' | 'Isolation';
  forceType: 'Push' | 'Pull' | 'Static';
  
  // Equipamiento
  equipment: ('Barbell' | 'Dumbbell' | 'Kettlebell' | 'Machine' | 'Cable' | 'Bodyweight' | 'Band' | 'Smith_Machine')[];
  
  // Metadatos de Entrenamiento
  difficulty: 1 | 2 | 3 | 4 | 5; // 1=Novato Total, 5=Atleta Elite
  impactLevel: 'Low' | 'Medium' | 'High'; // Para usuarios con sobrepeso/lesiones
  
  // Multimedia
  setupCues: string[]; // ["Retrae escápulas", "Pies firmes"] - Max 3 breves
  executionCues: string[]; // ["Baja controlado", "Toca el pecho", "Explota arriba"]
}
```

## Workflow de Creación Masiva

Para poblar la base de datos:

1.  **Definir el "Núcleo":** Empezar por los 50 ejercicios fundamentales (Big Lifts).
2.  **Generación de Variantes:** Usar la lógica de variantes para multiplicar la base.
    *   *Ejemplo:* "Sentadilla" -> Sentadilla Barra Alta, Sentadilla Barra Baja, Frontal, Goblet, Zercher, Hack, etc.
3.  **Validación Biomecánica:**
    *   ¿Coincide el patrón de movimiento?
    *   ¿Cambia el perfil de resistencia (ej: Bandas vs Cable)?
4.  **Generación de JSON:** Escribir el output directamente en formato JSON listo para Seed Script.

## Reglas de Calidad
- **Nombres en Español Neutro:** Evitar modismos locales (ej: usar "Mancuernas" no "Pesas de mano").
- **Cues Concisos:** Instrucciones de máximo 6 palabras. Directas al hueso.
- **Seguridad Primero:** Ante la duda de dificultad, redondear hacia arriba.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mens-git) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
