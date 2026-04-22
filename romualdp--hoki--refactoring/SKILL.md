---
name: refactoring-best-practices
description: Méthodologie TDD/BDD pour refactoring sûr et systématique. MANDATORY pour code improvement. À utiliser lors de refactoring, amélioration de code, ou quand l'utilisateur mentionne "refactor", "améliorer", "nettoyer le code", "simplifier". Use when this capability is needed.
metadata:
  author: romualdp
---

# Refactoring Best Practices

## 🎯 Mission

Appliquer des **pratiques de refactoring sûres** en suivant la méthodologie **TDD (Test-Driven Development)** et **BDD (Behavior-Driven Development)**.

## 🧐 Philosophie

**Refactoring ≠ Réécriture**

Un bon refactoring :
1. ✅ Améliore la structure du code SANS changer son comportement
2. ✅ Est guidé par les tests (RED → GREEN → REFACTOR)
3. ✅ Se fait en petites étapes incrémentales
4. ✅ Maintient tous les tests au vert
5. ✅ Rend le code plus lisible et maintenable

## 🔄 TDD Cycle: RED → GREEN → REFACTOR

### RED: Write a Failing Test

```typescript
// tests/unit/domain/entities/club.entity.spec.ts

describe('Club Entity', () => {
  describe('changeName()', () => {
    it('should update club name when valid', () => {
      // Arrange
      const club = Club.create('Old Name', 'Description', 'user-123');
      const newName = ClubName.create('New Name');

      // Act
      club.changeName(newName);

      // Assert
      expect(club.getName().getValue()).toBe('New Name');
    });
  });
});

// Run test
// ❌ FAIL: club.changeName is not a function
```

### GREEN: Write Minimal Code to Pass

```typescript
// domain/entities/club.entity.ts

export class Club {
  constructor(
    private readonly id: string,
    private name: ClubName,
    private description: string,
    private readonly userId: string,
  ) {}

  // Minimal implementation
  changeName(newName: ClubName): void {
    this.name = newName;
  }

  getName(): ClubName {
    return this.name;
  }
}

// Run test
// ✅ PASS
```

### REFACTOR: Improve Code Structure

```typescript
// domain/entities/club.entity.ts

export class Club {
  constructor(
    private readonly id: string,
    private name: ClubName,
    private description: string,
    private readonly userId: string,
  ) {}

  // Refactored with validation
  changeName(newName: ClubName): void {
    if (!newName) {
      throw new InvalidClubNameException('Club name cannot be null');
    }

    // Domain rule: Name must be different
    if (this.name.equals(newName)) {
      throw new ClubNameUnchangedException('New name is the same as current name');
    }

    this.name = newName;
  }

  getName(): ClubName {
    return this.name;
  }
}

// Run test
// ✅ PASS (still green after refactor)
```

## 🎭 BDD Pattern: Given-When-Then

### Structure BDD pour Tests Frontend

```typescript
// features/club-management/components/ClubCreationForm.spec.tsx

describe('ClubCreationForm', () => {
  describe('when submitting valid data', () => {
    it('should create club and show success message', () => {
      // GIVEN - Arrange
      const mockOnSuccess = jest.fn();
      const { getByLabelText, getByRole } = render(
        <ClubCreationForm onSuccess={mockOnSuccess} />
      );

      // WHEN - Act
      fireEvent.change(getByLabelText(/nom du club/i), {
        target: { value: 'Test Club' },
      });
      fireEvent.change(getByLabelText(/description/i), {
        target: { value: 'Test Description' },
      });
      fireEvent.click(getByRole('button', { name: /créer/i }));

      // THEN - Assert
      await waitFor(() => {
        expect(mockOnSuccess).toHaveBeenCalledWith(
          expect.objectContaining({
            name: 'Test Club',
            description: 'Test Description',
          })
        );
        expect(screen.getByText(/club créé avec succès/i)).toBeInTheDocument();
      });
    });
  });

  describe('when submitting invalid data', () => {
    it('should show validation error', () => {
      // GIVEN
      const { getByLabelText, getByRole } = render(<ClubCreationForm />);

      // WHEN - Submit with empty name
      fireEvent.click(getByRole('button', { name: /créer/i }));

      // THEN
      expect(screen.getByText(/le nom est requis/i)).toBeInTheDocument();
    });
  });
});
```

### Structure BDD Alternative: AAA (Arrange-Act-Assert)

```typescript
describe('CreateClubHandler', () => {
  it('should create club and return ID', async () => {
    // Arrange
    const command = new CreateClubCommand('Test Club', 'Description', 'user-123');
    const mockRepository = createMockRepository();
    const handler = new CreateClubHandler(mockRepository);

    // Act
    const clubId = await handler.execute(command);

    // Assert
    expect(clubId).toBeDefined();
    expect(typeof clubId).toBe('string');
    expect(mockRepository.create).toHaveBeenCalledTimes(1);
  });
});
```

**Quand utiliser Given-When-Then vs AAA**:
- **Given-When-Then**: Tests frontend, tests d'intégration, scénarios utilisateur
- **AAA**: Tests unitaires backend, tests de domaine, tests de handlers

## 📋 Refactoring Patterns

### 1. Extract Function

**Quand**: Fonction trop longue (>30 lignes), logique complexe imbriquée

```typescript
// ❌ AVANT - Fonction longue et complexe
export function ClubCreationForm() {
  const [step, setStep] = useState(1);
  const [formData, setFormData] = useState({});
  const [errors, setErrors] = useState({});

  const handleSubmit = async () => {
    // Validation
    const newErrors = {};
    if (!formData.name) newErrors.name = 'Required';
    if (!formData.description) newErrors.description = 'Required';
    if (formData.name && formData.name.length < 3) {
      newErrors.name = 'Min 3 characters';
    }
    setErrors(newErrors);
    if (Object.keys(newErrors).length > 0) return;

    // API call
    try {
      const result = await fetch('/api/clubs', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(formData),
      });
      const data = await result.json();
      if (result.ok) {
        router.push(`/clubs/${data.id}`);
      } else {
        setErrors({ api: data.message });
      }
    } catch (err) {
      setErrors({ api: 'Network error' });
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* 100+ lines of JSX */}
    </form>
  );
}
```

```typescript
// ✅ APRÈS - Fonctions extraites

// utils/club-validation.ts
export function validateClubData(data: ClubFormData): ValidationErrors {
  const errors: ValidationErrors = {};

  if (!data.name) {
    errors.name = 'Le nom est requis';
  } else if (data.name.length < 3) {
    errors.name = 'Le nom doit contenir au moins 3 caractères';
  }

  if (!data.description) {
    errors.description = 'La description est requise';
  }

  return errors;
}

// hooks/useClubCreation.ts
export function useClubCreation() {
  const [isLoading, setIsLoading] = useState(false);
  const [error, setError] = useState<string | null>(null);
  const router = useRouter();

  const createClub = async (data: ClubFormData) => {
    setIsLoading(true);
    setError(null);

    try {
      const result = await clubsApi.create(data);
      router.push(`/clubs/${result.id}`);
      return { success: true };
    } catch (err) {
      setError(err.message);
      return { success: false, error: err.message };
    } finally {
      setIsLoading(false);
    }
  };

  return { createClub, isLoading, error };
}

// components/ClubCreationForm.tsx
export function ClubCreationForm() {
  const [formData, setFormData] = useState({});
  const [errors, setErrors] = useState({});
  const { createClub, isLoading } = useClubCreation();

  const handleSubmit = async () => {
    const validationErrors = validateClubData(formData);
    setErrors(validationErrors);

    if (Object.keys(validationErrors).length === 0) {
      await createClub(formData);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Focused JSX */}
    </form>
  );
}
```

### 2. Extract Component

**Quand**: Composant trop long (>150 lignes), responsabilités multiples

```typescript
// ❌ AVANT - Composant monolithique
export function ClubDashboard({ clubId }: Props) {
  return (
    <div>
      <h1>Dashboard</h1>

      {/* Stats section - 50 lines */}
      <div className="grid grid-cols-4">
        <div>
          <h2>Membres</h2>
          <p>{stats.membersCount}</p>
        </div>
        <div>
          <h2>Équipes</h2>
          <p>{stats.teamsCount}</p>
        </div>
        {/* ... */}
      </div>

      {/* Members list - 80 lines */}
      <div>
        <h2>Membres</h2>
        <ul>
          {members.map(member => (
            <li key={member.id}>
              <img src={member.avatar} />
              <span>{member.name}</span>
              <button>Edit</button>
              <button>Remove</button>
            </li>
          ))}
        </ul>
      </div>

      {/* Quick actions - 40 lines */}
      <div>
        <h2>Actions rapides</h2>
        <button>Créer équipe</button>
        <button>Inviter membre</button>
        {/* ... */}
      </div>
    </div>
  );
}
```

```typescript
// ✅ APRÈS - Composants extraits

// components/ClubStats.tsx
export function ClubStats({ stats }: Props) {
  return (
    <div className="grid grid-cols-4 gap-4">
      <StatCard title="Membres" value={stats.membersCount} />
      <StatCard title="Équipes" value={stats.teamsCount} />
      <StatCard title="Matchs" value={stats.matchesCount} />
      <StatCard title="Victoires" value={stats.winsCount} />
    </div>
  );
}

// components/StatCard.tsx
export function StatCard({ title, value }: Props) {
  return (
    <div className="rounded-lg border p-4">
      <h2 className="text-sm text-muted-foreground">{title}</h2>
      <p className="text-2xl font-bold">{value}</p>
    </div>
  );
}

// components/MembersList.tsx
export function MembersList({ members }: Props) {
  return (
    <div>
      <h2 className="mb-4 text-lg font-semibold">Membres</h2>
      <ul className="space-y-2">
        {members.map(member => (
          <MemberCard key={member.id} member={member} />
        ))}
      </ul>
    </div>
  );
}

// components/MemberCard.tsx
export function MemberCard({ member }: Props) {
  return (
    <li className="flex items-center gap-4 rounded-lg border p-3">
      <img src={member.avatar} alt={member.name} className="h-10 w-10 rounded-full" />
      <span className="flex-1">{member.name}</span>
      <button className="btn-secondary">Edit</button>
      <button className="btn-danger">Remove</button>
    </li>
  );
}

// components/QuickActions.tsx
export function QuickActions({ clubId }: Props) {
  return (
    <div>
      <h2 className="mb-4 text-lg font-semibold">Actions rapides</h2>
      <div className="space-y-2">
        <button className="btn-primary w-full">Créer une équipe</button>
        <button className="btn-secondary w-full">Inviter un membre</button>
        <button className="btn-secondary w-full">Planifier un match</button>
      </div>
    </div>
  );
}

// pages/ClubDashboard.tsx (THIN)
export function ClubDashboard({ clubId }: Props) {
  const { club, stats, members } = useClubDashboard(clubId);

  return (
    <div>
      <h1>{club.name} - Dashboard</h1>

      <ClubStats stats={stats} />
      <MembersList members={members} />
      <QuickActions clubId={clubId} />
    </div>
  );
}
```

### 3. Remove Duplication (DRY)

**Quand**: Même logique répétée dans plusieurs endroits

```typescript
// ❌ AVANT - Duplication
export class CreateClubHandler {
  async execute(command: CreateClubCommand): Promise<string> {
    if (!command.name || command.name.trim().length === 0) {
      throw new ValidationException('Name is required');
    }
    if (command.name.length < 3) {
      throw new ValidationException('Name min 3 chars');
    }
    if (command.name.length > 100) {
      throw new ValidationException('Name max 100 chars');
    }
    // ...
  }
}

export class UpdateClubHandler {
  async execute(command: UpdateClubCommand): Promise<void> {
    if (!command.name || command.name.trim().length === 0) {
      throw new ValidationException('Name is required');
    }
    if (command.name.length < 3) {
      throw new ValidationException('Name min 3 chars');
    }
    if (command.name.length > 100) {
      throw new ValidationException('Name max 100 chars');
    }
    // ...
  }
}
```

```typescript
// ✅ APRÈS - Logique extraite dans Value Object

// domain/value-objects/club-name.vo.ts
export class ClubName {
  private constructor(private readonly value: string) {
    this.validate();
  }

  static create(value: string): ClubName {
    return new ClubName(value);
  }

  private validate(): void {
    if (!this.value || this.value.trim().length === 0) {
      throw new InvalidClubNameException('Name is required');
    }
    if (this.value.length < 3) {
      throw new InvalidClubNameException('Name must be at least 3 characters');
    }
    if (this.value.length > 100) {
      throw new InvalidClubNameException('Name cannot exceed 100 characters');
    }
  }

  getValue(): string {
    return this.value;
  }

  equals(other: ClubName): boolean {
    return this.value === other.value;
  }
}

// Handlers simplement utilisent ClubName
export class CreateClubHandler {
  async execute(command: CreateClubCommand): Promise<string> {
    const clubName = ClubName.create(command.name); // Validation automatique
    const club = Club.create(clubName, command.description, command.userId);
    // ...
  }
}
```

### 4. Simplify Nested Structures

**Quand**: Imbrications excessives (>3 niveaux), conditions complexes

```typescript
// ❌ AVANT - Imbrications excessives
export async function processSubscription(clubId: string, planId: string) {
  const club = await clubRepository.findById(clubId);

  if (club) {
    const subscription = club.getSubscription();

    if (subscription) {
      if (subscription.isActive()) {
        const plan = await planRepository.findById(planId);

        if (plan) {
          if (plan.getPrice() > 0) {
            const payment = await createPayment(club, plan);

            if (payment.success) {
              subscription.upgrade(plan);
              await clubRepository.update(club);
              return { success: true };
            } else {
              return { success: false, error: 'Payment failed' };
            }
          } else {
            return { success: false, error: 'Invalid plan price' };
          }
        } else {
          return { success: false, error: 'Plan not found' };
        }
      } else {
        return { success: false, error: 'Subscription not active' };
      }
    } else {
      return { success: false, error: 'No subscription' };
    }
  } else {
    return { success: false, error: 'Club not found' };
  }
}
```

```typescript
// ✅ APRÈS - Early returns + fonctions extraites

export async function processSubscription(
  clubId: string,
  planId: string
): Promise<Result> {
  // Early returns for validation
  const club = await clubRepository.findById(clubId);
  if (!club) {
    return failure('Club not found');
  }

  const subscription = club.getSubscription();
  if (!subscription) {
    return failure('No subscription');
  }

  if (!subscription.isActive()) {
    return failure('Subscription not active');
  }

  const plan = await planRepository.findById(planId);
  if (!plan) {
    return failure('Plan not found');
  }

  if (plan.getPrice() <= 0) {
    return failure('Invalid plan price');
  }

  // Happy path
  return await upgradeSubscription(club, subscription, plan);
}

async function upgradeSubscription(
  club: Club,
  subscription: Subscription,
  plan: Plan
): Promise<Result> {
  const payment = await createPayment(club, plan);

  if (!payment.success) {
    return failure('Payment failed');
  }

  subscription.upgrade(plan);
  await clubRepository.update(club);

  return success();
}

// Helpers
function success(): Result {
  return { success: true };
}

function failure(error: string): Result {
  return { success: false, error };
}
```

### 5. Replace Long Function with Smaller Ones

**Quand**: Fonction >30 lignes, responsabilités multiples

```typescript
// ❌ AVANT - Fonction longue
export async function registerToTraining(
  userId: string,
  trainingId: string
): Promise<Result> {
  // Fetch user
  const user = await userRepository.findById(userId);
  if (!user) return { success: false, error: 'User not found' };

  // Fetch training
  const training = await trainingRepository.findById(trainingId);
  if (!training) return { success: false, error: 'Training not found' };

  // Check if training is full
  const registrations = await registrationRepository.findByTraining(trainingId);
  if (registrations.length >= training.maxParticipants) {
    return { success: false, error: 'Training is full' };
  }

  // Check if user already registered
  const existingRegistration = registrations.find(r => r.userId === userId);
  if (existingRegistration) {
    return { success: false, error: 'Already registered' };
  }

  // Check user subscription
  const subscription = await subscriptionRepository.findByUser(userId);
  if (!subscription || !subscription.isActive()) {
    return { success: false, error: 'No active subscription' };
  }

  // Create registration
  const registration = new Registration(userId, trainingId, new Date());
  await registrationRepository.create(registration);

  // Send notification
  await notificationService.send(userId, {
    title: 'Registration confirmed',
    message: `You are registered for ${training.title}`,
  });

  return { success: true };
}
```

```typescript
// ✅ APRÈS - Fonctions petites et focalisées

export async function registerToTraining(
  userId: string,
  trainingId: string
): Promise<Result> {
  // Validation
  const validationResult = await validateRegistration(userId, trainingId);
  if (!validationResult.success) {
    return validationResult;
  }

  // Registration
  await createRegistration(userId, trainingId);

  // Notification
  await sendRegistrationConfirmation(userId, trainingId);

  return { success: true };
}

async function validateRegistration(
  userId: string,
  trainingId: string
): Promise<Result> {
  const user = await userRepository.findById(userId);
  if (!user) return failure('User not found');

  const training = await trainingRepository.findById(trainingId);
  if (!training) return failure('Training not found');

  const isFull = await isTrainingFull(trainingId, training.maxParticipants);
  if (isFull) return failure('Training is full');

  const alreadyRegistered = await isUserRegistered(userId, trainingId);
  if (alreadyRegistered) return failure('Already registered');

  const hasActiveSubscription = await userHasActiveSubscription(userId);
  if (!hasActiveSubscription) return failure('No active subscription');

  return success();
}

async function isTrainingFull(
  trainingId: string,
  maxParticipants: number
): Promise<boolean> {
  const registrations = await registrationRepository.findByTraining(trainingId);
  return registrations.length >= maxParticipants;
}

async function isUserRegistered(
  userId: string,
  trainingId: string
): Promise<boolean> {
  const registrations = await registrationRepository.findByTraining(trainingId);
  return registrations.some(r => r.userId === userId);
}

async function userHasActiveSubscription(userId: string): Promise<boolean> {
  const subscription = await subscriptionRepository.findByUser(userId);
  return subscription !== null && subscription.isActive();
}

async function createRegistration(
  userId: string,
  trainingId: string
): Promise<void> {
  const registration = new Registration(userId, trainingId, new Date());
  await registrationRepository.create(registration);
}

async function sendRegistrationConfirmation(
  userId: string,
  trainingId: string
): Promise<void> {
  const training = await trainingRepository.findById(trainingId);

  await notificationService.send(userId, {
    title: 'Registration confirmed',
    message: `You are registered for ${training.title}`,
  });
}
```

## ✅ Refactoring Checklist

### Avant de Refactor
- [ ] Tous les tests existants passent (✅ GREEN)
- [ ] Coverage >80% pour la zone à refactorer
- [ ] Comprendre le comportement actuel du code
- [ ] Identifier les "code smells" (duplication, complexité, longueur)

### Pendant le Refactor
- [ ] Refactorer en petites étapes (commits fréquents)
- [ ] Run tests après CHAQUE changement
- [ ] Si un test fail, REVERT immédiatement
- [ ] Ne PAS changer le comportement (pas de nouvelles features)
- [ ] Maintenir les tests au vert (✅ GREEN en permanence)

### Après le Refactor
- [ ] Tous les tests passent
- [ ] Code coverage maintenu ou amélioré
- [ ] Code plus lisible et maintenable
- [ ] Duplication éliminée
- [ ] Complexité réduite (cyclomatic complexity)
- [ ] Noms de variables/fonctions clairs

## 🚨 Erreurs Courantes

### 1. Refactor Sans Tests

```markdown
❌ MAUVAIS
Dev: "Je refactor ce code"
[Modifie 200 lignes]
[Aucun test pour vérifier le comportement]

✅ BON
Dev: "Je vérifie que tous les tests passent"
Dev: "Je refactor en petites étapes"
Dev: "Je run les tests après chaque étape"
```

### 2. Changer le Comportement

```markdown
❌ MAUVAIS - Refactor + Nouvelle Feature
function createClub(data) {
  // Refactor logic
  // + Ajouter validation email
  // + Ajouter envoi notification
}

✅ BON - Refactor Seulement
function createClub(data) {
  // Refactor logic UNIQUEMENT
  // Pas de nouvelle feature
}
// Nouvelle feature dans un autre commit
```

### 3. Grandes Étapes

```markdown
❌ MAUVAIS
[1 commit] "Refactor entire club-management module"
- 2000 lines changed
- 50 files modified

✅ BON
[commit 1] "Extract ClubName value object"
[commit 2] "Extract validateClubData utility"
[commit 3] "Simplify CreateClubHandler"
[commit 4] "Remove duplication in mappers"
```

### 4. Oublier de Run Tests

```markdown
❌ MAUVAIS
Dev modifie le code
Dev commit
Dev continue

✅ BON
Dev modifie le code
Dev run tests
✅ All pass
Dev commit
Dev continue
```

## 📚 Skills Complémentaires

- **ddd-testing** : Standards de tests pour DDD
- **zero-warnings** : Règles de qualité de code
- **bug-finder** : Identifier les bugs pendant refactoring
- **debugger** : Debugging si tests cassent

---

**Rappel CRITIQUE** : **RED → GREEN → REFACTOR**. Toujours avoir les tests au vert avant et après refactoring.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/romualdp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
