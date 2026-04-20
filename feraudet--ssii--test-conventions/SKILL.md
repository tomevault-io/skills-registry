---
name: test-conventions
description: Standards pour écrire et organiser les tests. Use when "write tests", "add tests", "test coverage", "unit test". Use when this capability is needed.
metadata:
  author: feraudet
---

# Test Conventions

## Purpose
Définir les conventions de tests pour le projet consultant-manager.

## Stack de Test (À Implémenter)

### Backend
- **Framework**: Vitest (recommandé pour monorepo TS)
- **Assertions**: expect de Vitest
- **Mocking**: vi.mock() de Vitest
- **Base de données**: SQLite in-memory pour tests

### Frontend
- **Framework**: Vitest + React Testing Library
- **Rendering**: @testing-library/react
- **User interactions**: @testing-library/user-event
- **Mocking API**: MSW (Mock Service Worker)

## Structure des Tests

### Organisation des Fichiers

```
backend/
├── src/
│   ├── controllers/
│   │   ├── consultants.ts
│   │   └── consultants.test.ts          # À côté du fichier source
│   ├── utils/
│   │   ├── status.ts
│   │   └── status.test.ts
└── tests/
    ├── integration/
    │   ├── consultants.api.test.ts      # Tests d'intégration API
    │   └── missions.api.test.ts
    └── setup.ts                          # Configuration globale

frontend/
├── src/
│   ├── components/
│   │   ├── ConsultantForm.tsx
│   │   └── ConsultantForm.test.tsx
│   ├── pages/
│   │   ├── Dashboard.tsx
│   │   └── Dashboard.test.tsx
└── tests/
    └── setup.ts
```

## Conventions de Nommage

### Fichiers
- Tests unitaires: `{filename}.test.ts` ou `{filename}.spec.ts`
- Tests d'intégration: `{feature}.integration.test.ts`
- Tests E2E: `{feature}.e2e.test.ts`

### Describe Blocks
```typescript
describe('ConsultantsController', () => {
  describe('getAllConsultants', () => {
    it('should return all consultants', async () => {
      // ...
    });

    it('should filter by status when provided', async () => {
      // ...
    });
  });
});
```

### Test Names
Format: `should {expected behavior} when {condition}`

✅ Bon:
- `should return 404 when consultant not found`
- `should calculate revenue correctly`
- `should update consultant status based on active mission`

❌ Mauvais:
- `test 1`
- `works`
- `consultant test`

## Tests Backend

### Unit Tests: Controllers

```typescript
// consultants.test.ts
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { getAllConsultants } from './consultants';
import { prisma } from '../index';

// Mock Prisma
vi.mock('../index', () => ({
  prisma: {
    consultant: {
      findMany: vi.fn(),
    },
  },
}));

describe('ConsultantsController', () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  describe('getAllConsultants', () => {
    it('should return all consultants with calculated status', async () => {
      // Arrange
      const mockConsultants = [
        {
          id: '1',
          nom: 'Dupont',
          prenom: 'Jean',
          missions: []
        }
      ];
      vi.mocked(prisma.consultant.findMany).mockResolvedValue(mockConsultants);

      const req = { query: {} } as any;
      const res = {
        json: vi.fn(),
        status: vi.fn().mockReturnThis()
      } as any;

      // Act
      await getAllConsultants(req, res);

      // Assert
      expect(res.json).toHaveBeenCalledWith(
        expect.arrayContaining([
          expect.objectContaining({
            id: '1',
            nom: 'Dupont'
          })
        ])
      );
    });
  });
});
```

### Integration Tests: API Routes

```typescript
// consultants.api.test.ts
import { describe, it, expect, beforeAll, afterAll } from 'vitest';
import request from 'supertest';
import { app } from '../src/index';
import { prisma } from '../src/index';

describe('Consultants API', () => {
  beforeAll(async () => {
    // Setup: créer des données de test
    await prisma.consultant.create({
      data: {
        nom: 'Test',
        prenom: 'User',
        email: 'test@example.com',
        competences: JSON.stringify(['React']),
        tjm: 500,
        statut: 'DISPONIBLE'
      }
    });
  });

  afterAll(async () => {
    // Cleanup: supprimer les données de test
    await prisma.consultant.deleteMany({});
    await prisma.$disconnect();
  });

  describe('GET /api/consultants', () => {
    it('should return 200 and list of consultants', async () => {
      const response = await request(app)
        .get('/api/consultants')
        .expect(200);

      expect(response.body).toBeInstanceOf(Array);
      expect(response.body.length).toBeGreaterThan(0);
    });

    it('should filter by status', async () => {
      const response = await request(app)
        .get('/api/consultants?statut=DISPONIBLE')
        .expect(200);

      expect(response.body.every((c: any) => c.statut === 'DISPONIBLE')).toBe(true);
    });
  });
});
```

### Unit Tests: Utilities

```typescript
// status.test.ts
import { describe, it, expect } from 'vitest';
import { calculateConsultantStatus, calculateRevenue } from './status';

describe('Status Utils', () => {
  describe('calculateConsultantStatus', () => {
    it('should return EN_MISSION when consultant has active mission', () => {
      const consultant = {
        id: '1',
        statut: 'DISPONIBLE',
        missions: [
          {
            dateDebut: new Date('2026-01-01'),
            dateFin: new Date('2026-12-31')
          }
        ]
      } as any;

      const status = calculateConsultantStatus(consultant);

      expect(status).toBe('EN_MISSION');
    });

    it('should return manual status when no active mission', () => {
      const consultant = {
        id: '1',
        statut: 'EN_CONGES',
        missions: []
      } as any;

      const status = calculateConsultantStatus(consultant);

      expect(status).toBe('EN_CONGES');
    });
  });

  describe('calculateRevenue', () => {
    it('should calculate revenue correctly', () => {
      const tjm = 500;
      const dateDebut = new Date('2026-01-01');
      const dateFin = new Date('2026-01-10'); // 10 jours

      const revenue = calculateRevenue(tjm, dateDebut, dateFin);

      expect(revenue).toBe(5000); // 500 * 10
    });
  });
});
```

## Tests Frontend

### Component Tests

```typescript
// ConsultantForm.test.tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import ConsultantForm from './ConsultantForm';

describe('ConsultantForm', () => {
  it('should render form fields', () => {
    render(<ConsultantForm consultant={null} onClose={vi.fn()} />);

    expect(screen.getByLabelText(/prénom/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/nom/i)).toBeInTheDocument();
    expect(screen.getByLabelText(/email/i)).toBeInTheDocument();
  });

  it('should submit form with valid data', async () => {
    const mockOnClose = vi.fn();
    const user = userEvent.setup();

    render(<ConsultantForm consultant={null} onClose={mockOnClose} />);

    await user.type(screen.getByLabelText(/prénom/i), 'Jean');
    await user.type(screen.getByLabelText(/nom/i), 'Dupont');
    await user.type(screen.getByLabelText(/email/i), 'jean@example.com');
    await user.type(screen.getByLabelText(/tjm/i), '500');
    await user.type(screen.getByLabelText(/compétences/i), 'React, TypeScript');

    await user.click(screen.getByRole('button', { name: /enregistrer/i }));

    await waitFor(() => {
      expect(mockOnClose).toHaveBeenCalled();
    });
  });

  it('should display validation errors', async () => {
    const user = userEvent.setup();

    render(<ConsultantForm consultant={null} onClose={vi.fn()} />);

    // Submit sans remplir
    await user.click(screen.getByRole('button', { name: /enregistrer/i }));

    // HTML5 validation should prevent submission
    expect(screen.getByLabelText(/prénom/i)).toBeInvalid();
  });
});
```

### Page Tests

```typescript
// Dashboard.test.tsx
import { describe, it, expect, beforeEach, vi } from 'vitest';
import { render, screen, waitFor } from '@testing-library/react';
import { BrowserRouter } from 'react-router-dom';
import Dashboard from './Dashboard';
import { dashboardAPI } from '../services/api';

vi.mock('../services/api');

describe('Dashboard', () => {
  beforeEach(() => {
    vi.clearAllMocks();
  });

  it('should display loading state initially', () => {
    vi.mocked(dashboardAPI.getStats).mockReturnValue(
      new Promise(() => {}) // Never resolves
    );

    render(
      <BrowserRouter>
        <Dashboard />
      </BrowserRouter>
    );

    expect(screen.getByText(/chargement/i)).toBeInTheDocument();
  });

  it('should display dashboard stats when loaded', async () => {
    const mockStats = {
      consultants: {
        total: 10,
        disponibles: 5,
        enMission: 3,
        enConges: 2,
        indisponibles: 0,
        tauxOccupation: 30
      },
      missionsEndingSoon: [],
      missionsActives: []
    };

    vi.mocked(dashboardAPI.getStats).mockResolvedValue(mockStats);

    render(
      <BrowserRouter>
        <Dashboard />
      </BrowserRouter>
    );

    await waitFor(() => {
      expect(screen.getByText('5')).toBeInTheDocument(); // disponibles
      expect(screen.getByText('3')).toBeInTheDocument(); // en mission
      expect(screen.getByText('30%')).toBeInTheDocument(); // taux
    });
  });
});
```

## Mocking

### Mock API Calls
```typescript
import { vi } from 'vitest';
import * as api from '../services/api';

vi.mock('../services/api', () => ({
  consultantsAPI: {
    getAll: vi.fn(),
    create: vi.fn(),
  },
}));
```

### Mock Prisma
```typescript
vi.mock('@prisma/client', () => ({
  PrismaClient: vi.fn(() => ({
    consultant: {
      findMany: vi.fn(),
      create: vi.fn(),
    },
  })),
}));
```

## Couverture de Tests

### Objectifs
- **Fonctions utilitaires**: 100%
- **Controllers/Services**: 80%+
- **Composants**: 70%+
- **Pages**: 60%+

### Commandes
```bash
# Backend
cd backend
npm run test              # Run tests
npm run test:coverage     # With coverage

# Frontend
cd frontend
npm run test
npm run test:coverage
```

## Checklist Avant Commit

- [ ] Tous les tests passent
- [ ] Nouveaux tests ajoutés pour nouveau code
- [ ] Couverture maintenue ou améliorée
- [ ] Pas de tests skippés sans raison (no `.skip`)
- [ ] Pas de tests en `.only` (oubli)
- [ ] Tests rapides (< 100ms par test unitaire)
- [ ] Mocks nettoyés avec `beforeEach`

## Principes Généraux

1. **AAA Pattern**: Arrange, Act, Assert
2. **Tests indépendants**: Chaque test peut tourner seul
3. **Pas de dépendances externes**: Mock les APIs, DB, etc.
4. **Tests lisibles**: Comme de la documentation
5. **Tests rapides**: Unitaires < 100ms, intégration < 1s

## Red Flags

🚨 **À éviter:**
- Tests qui dépendent de l'ordre d'exécution
- Tests avec `setTimeout` arbitraires
- Tests qui touchent la vraie base de données
- Tests qui font de vrais appels HTTP
- Tests avec logique complexe (tester les tests?)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/feraudet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
