---
name: code-review
description: Standards et checklist pour la revue de code. Use when "review", "check my code", "validate changes", "code quality". Use when this capability is needed.
metadata:
  author: feraudet
---

# Code Review Standards

## Purpose
Guide Claude dans la revue de code selon les standards du projet de gestion des consultants.

## Standards Généraux

### Architecture & Organisation
- [ ] Le code respecte l'architecture monorepo (backend/frontend séparés)
- [ ] Les changements sont dans le bon workspace
- [ ] Les types partagés sont dans `/shared` si réutilisés
- [ ] Pas de couplage entre backend et frontend (API uniquement)

### Qualité du Code
- [ ] Pas de code dupliqué (DRY principle)
- [ ] Fonctions courtes et single-responsibility
- [ ] Nommage clair et descriptif (pas d'abréviations cryptiques)
- [ ] Pas de magic numbers (utiliser des constantes nommées)
- [ ] Gestion d'erreurs appropriée (try/catch, validation)

### TypeScript
- [ ] Types explicites (pas de `any` sauf justifié)
- [ ] Interfaces pour les objets complexes
- [ ] Types partagés entre backend/frontend dans `/shared/types`
- [ ] Utilisation de Zod pour validation côté backend

### Sécurité
- [ ] Validation des entrées utilisateur (Zod côté backend)
- [ ] Pas de données sensibles en dur dans le code
- [ ] CORS configuré correctement
- [ ] Pas d'injection SQL (utiliser Prisma uniquement)
- [ ] Pas d'XSS (échapper les données affichées)

### Performance
- [ ] Pas de requêtes N+1 (utiliser `include` Prisma)
- [ ] Pagination pour les grandes listes
- [ ] Indexes appropriés en base de données
- [ ] Optimisation des re-renders React (memo si nécessaire)

### Business Logic (Domaine Consultants)
- [ ] Un consultant ne peut avoir qu'une mission active à la fois
- [ ] Validation: dateFin > dateDebut
- [ ] Calculs automatiques (durée, revenus) côté backend
- [ ] Statut consultant calculé en temps réel basé sur missions

## Checklist Backend (Express/Prisma)

### Routes & Controllers
- [ ] Routes RESTful (GET, POST, PUT, DELETE)
- [ ] Validation Zod sur toutes les entrées
- [ ] Gestion d'erreurs avec try/catch
- [ ] Status codes HTTP appropriés (200, 201, 400, 404, 500)
- [ ] Réponses JSON consistantes

### Prisma
- [ ] Utilisation de `include` pour charger relations
- [ ] Transactions pour opérations multiples
- [ ] Soft delete préservé (si applicable)
- [ ] Champs JSON parsés correctement (competences, etc.)

### API Design
- [ ] Endpoints cohérents avec la spec CLAUDE.md
- [ ] Filtres via query params (?statut=, ?search=)
- [ ] Dates en ISO 8601 format
- [ ] Calculs métier (revenus, durée) retournés par API

## Checklist Frontend (React)

### Composants
- [ ] Composants fonctionnels (pas de classes)
- [ ] Props typées avec interfaces TypeScript
- [ ] Pas de logique métier dans les composants (dans services)
- [ ] État local minimal (useState)
- [ ] Effets de bord dans useEffect avec dépendances correctes

### Formulaires
- [ ] Validation côté client avant soumission
- [ ] États de chargement (loading, error)
- [ ] Messages d'erreur clairs pour l'utilisateur
- [ ] Feedback visuel (toasts, notifications)

### API Calls
- [ ] Appels via service API centralisé (`services/api.ts`)
- [ ] Gestion d'erreurs avec try/catch
- [ ] Loading states
- [ ] Types de réponse définis

### Tailwind CSS
- [ ] Classes utilitaires uniquement (pas de CSS custom)
- [ ] Responsive design (sm:, md:, lg:)
- [ ] Accessibilité (ARIA labels si nécessaire)
- [ ] Cohérence visuelle avec le design system

## Red Flags (À Corriger Immédiatement)

🚨 **Critique:**
- Code avec `any` partout
- Pas de validation des entrées
- Dates manipulées comme strings (utiliser date-fns)
- Données sensibles en dur (.env manquants)
- Requêtes SQL brutes (utiliser Prisma)

⚠️ **Important:**
- Code dupliqué (extraire en fonction)
- Fonctions de plus de 50 lignes
- Composants React de plus de 200 lignes
- Pas de gestion d'erreurs
- Console.log oubliés

## Exemples

### ✅ Bon: Validation avec Zod
```typescript
const consultantSchema = z.object({
  nom: z.string().min(1),
  prenom: z.string().min(1),
  email: z.string().email(),
  tjm: z.number().positive()
});

const validatedData = consultantSchema.parse(req.body);
```

### ❌ Mauvais: Pas de validation
```typescript
const consultant = await prisma.consultant.create({
  data: req.body  // Dangereux!
});
```

### ✅ Bon: Calcul côté backend
```typescript
export const createMission = async (req: Request, res: Response) => {
  const mission = await prisma.mission.create({ data });
  res.json({
    ...mission,
    revenusGeneres: calculateRevenue(mission.tjm, mission.dateDebut, mission.dateFin),
    dureeJours: calculateDuration(mission.dateDebut, mission.dateFin)
  });
};
```

### ❌ Mauvais: Calcul côté frontend uniquement
```typescript
// Ne pas faire les calculs métier dans le frontend!
const revenus = (dateFin - dateDebut) * tjm;
```

## Processus de Review

1. **Lire CLAUDE.md** - Comprendre les règles métier
2. **Vérifier l'architecture** - Bon workspace, pas de couplage
3. **Valider la sécurité** - Pas de failles évidentes
4. **Tester mentalement** - Le code fait-il ce qu'il doit?
5. **Suggérer améliorations** - Priorité: sécurité > bugs > qualité > style

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/feraudet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
