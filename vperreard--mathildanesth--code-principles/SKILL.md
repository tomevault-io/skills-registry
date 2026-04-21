---
name: code-principles
description: > Use when this capability is needed.
metadata:
  author: vperreard
---

# Code Principles - KISS YAGNI SRP DRY

**Purpose**: Principes de design pour un code propre et maintenable
**Enforcement**: Rappel systématique (non-bloquant)

---

## 🎯 Les 4 Principes

### KISS - Keep It Simple, Stupid

> La simplicité doit être un objectif clé.

```typescript
// ❌ KISS violé - Sur-ingénierie
function isAdult(age: number): boolean {
  const ADULT_AGE_THRESHOLD = 18;
  const ageValidator = new AgeValidator(ADULT_AGE_THRESHOLD);
  return ageValidator.validate(age).isValid;
}

// ✅ KISS - Simple et direct
function isAdult(age: number): boolean {
  return age >= 18;
}
```

**Signal d'alerte** : Si tu crées une classe pour une opération simple → KISS violé

---

### YAGNI - You Aren't Gonna Need It

> N'implémente jamais une fonctionnalité avant d'en avoir besoin.

```typescript
// ❌ YAGNI violé - "Au cas où..."
interface User {
  id: string;
  name: string;
  email: string;
  futureFeatureFlag?: boolean;      // Pas demandé!
  experimentalSettings?: object;     // Pas demandé!
}

// ✅ YAGNI - Seulement le nécessaire
interface User {
  id: string;
  name: string;
  email: string;
}
```

**Signal d'alerte** : Paramètre optionnel "pour plus tard" → YAGNI violé

---

### SRP - Single Responsibility Principle

> Une fonction/classe = UNE seule raison de changer.

```typescript
// ❌ SRP violé - Fait tout
class UserManager {
  createUser() { }
  sendWelcomeEmail() { }     // Responsabilité email
  generatePdfReport() { }    // Responsabilité PDF
  validatePayment() { }      // Responsabilité paiement
}

// ✅ SRP - Séparation des responsabilités
class UserService { createUser() { } }
class EmailService { sendWelcomeEmail() { } }
class ReportService { generatePdfReport() { } }
class PaymentService { validatePayment() { } }
```

**Signal d'alerte** :
- Fonction > 50 lignes → Probablement SRP violé
- Description avec "ET" → SRP violé

---

### DRY - Don't Repeat Yourself

> Chaque connaissance = UNE représentation unique.

```typescript
// ❌ DRY violé - Duplication
function calculateOrderTotal(items: Item[]) {
  return items.reduce((t, i) => t + i.price * i.qty * 1.2, 0);
}
function calculateCartTotal(items: Item[]) {
  return items.reduce((t, i) => t + i.price * i.qty * 1.2, 0); // Dupliqué!
}

// ✅ DRY - Source unique
const TVA_RATE = 1.2;
function calculateTotal(items: Item[]): number {
  return items.reduce((t, i) => t + i.price * i.qty * TVA_RATE, 0);
}
```

**Signal d'alerte** : Code copié-collé → DRY violé

---

## 🎯 Checklist Avant Modification

Avant chaque `Write` ou `Edit`, vérifier :

- [ ] **KISS** : Solution la plus simple possible ?
- [ ] **YAGNI** : Tout le code est nécessaire maintenant ?
- [ ] **SRP** : Chaque fonction fait une seule chose ?
- [ ] **DRY** : Pas de duplication de logique ?

---

## ⚖️ Équilibre des Principes

**Tension KISS vs DRY** :
Parfois 3 lignes dupliquées valent mieux qu'une abstraction prématurée.

```typescript
// Parfois OK de dupliquer (KISS > DRY)
// Si l'abstraction ajoute plus de complexité que la duplication
```

**Règle** : Dupliquer 2x = OK, 3x = factoriser

---

## 🔧 Application dans Mathildanesth

### Services (SRP)

```typescript
// ❌ Service fourre-tout
class PlanningService {
  generatePlanning() { }
  sendNotification() { }      // → NotificationService
  exportToPdf() { }           // → ExportService
  validateLeaves() { }        // → LeaveService
}

// ✅ Services séparés
// src/modules/planning/services/PlanningGeneratorService.ts
// src/modules/notifications/services/NotificationService.ts
// src/modules/export/services/ExportService.ts
```

### Composants React (KISS + SRP)

```tsx
// ❌ Composant > 200 lignes avec logique métier
function PlanningPage() {
  // 50 lignes de state
  // 100 lignes de logique
  // 150 lignes de JSX
}

// ✅ Composant léger + hooks + sous-composants
function PlanningPage() {
  const { data, handlers } = usePlanningLogic();
  return (
    <PlanningLayout>
      <PlanningHeader {...handlers} />
      <PlanningGrid data={data} />
    </PlanningLayout>
  );
}
```

### APIs (DRY)

```typescript
// ❌ Validation dupliquée dans chaque endpoint
// POST /api/planning
const schema1 = z.object({ date: z.string(), siteId: z.string() });
// POST /api/assignments
const schema2 = z.object({ date: z.string(), siteId: z.string() }); // Dupliqué!

// ✅ Schémas partagés
// src/lib/validations/common.ts
export const dateRangeSchema = z.object({
  date: z.string().datetime(),
  siteId: z.string().uuid()
});
```

---

## 📚 Progressive Resources

Pour exemples détaillés et cas avancés :
- **`resources/examples-detailles.md`** - Exemples complets par contexte Mathildanesth

---

**Maintained by**: Mathildanesth Team
**Last Update**: Décembre 2025

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vperreard) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
