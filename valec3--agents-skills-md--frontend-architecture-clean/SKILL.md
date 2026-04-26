---
name: frontend-architecture-clean
description: Standardized guidelines for implementing Clean Architecture in frontend applications. Use when building maintainable, testable applications with clear separation of concerns and dependency rules. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Architecture Clean

## When to use this skill
- Building complex business applications
- Need for high testability
- Multiple data sources (APIs, local storage, etc.)
- Long-term maintainability is critical

## Workflow
- [ ] Define domain entities and business rules
- [ ] Create use cases (application layer)
- [ ] Implement adapters for external services
- [ ] Keep UI layer thin (presentation only)
- [ ] Enforce dependency direction (inward only)

## Instructions

### Layer Structure

```
src/
├── domain/
│   ├── entities/
│   │   └── User.ts         # Pure business objects
│   ├── repositories/
│   │   └── IUserRepository.ts  # Interfaces
│   └── usecases/
│       └── GetUserProfile.ts
├── application/
│   ├── ports/              # Interfaces for adapters
│   └── services/
├── infrastructure/
│   ├── api/
│   │   └── UserApiRepository.ts  # Implements IUserRepository
│   ├── storage/
│   └── config/
└── presentation/
    ├── components/
    ├── pages/
    └── hooks/
```

### Key Principles

**1. Dependency Rule**
Dependencies point **inward** only:
```
Presentation → Application → Domain
Infrastructure → Application → Domain
```

**2. Domain Layer (Core)**
```typescript
// domain/entities/User.ts
export class User {
  constructor(
    public readonly id: string,
    public readonly email: string,
    private _name: string
  ) {}

  updateName(newName: string): void {
    if (newName.length < 2) {
      throw new Error('Name too short');
    }
    this._name = newName;
  }

  get name(): string {
    return this._name;
  }
}
```

**3. Repository Interface**
```typescript
// domain/repositories/IUserRepository.ts
export interface IUserRepository {
  findById(id: string): Promise<User | null>;
  save(user: User): Promise<void>;
}
```

**4. Use Case**
```typescript
// domain/usecases/GetUserProfile.ts
export class GetUserProfile {
  constructor(private userRepo: IUserRepository) {}

  async execute(userId: string): Promise<User> {
    const user = await this.userRepo.findById(userId);
    if (!user) throw new Error('User not found');
    return user;
  }
}
```

**5. Infrastructure Adapter**
```typescript
// infrastructure/api/UserApiRepository.ts
export class UserApiRepository implements IUserRepository {
  async findById(id: string): Promise<User | null> {
    const response = await fetch(`/api/users/${id}`);
    const data = await response.json();
    return new User(data.id, data.email, data.name);
  }

  async save(user: User): Promise<void> {
    await fetch(`/api/users/${user.id}`, {
      method: 'PUT',
      body: JSON.stringify(user)
    });
  }
}
```

**6. Presentation (React Hook)**
```typescript
// presentation/hooks/useUserProfile.ts
export function useUserProfile(userId: string) {
  const [user, setUser] = useState<User | null>(null);
  
  useEffect(() => {
    const useCase = new GetUserProfile(new UserApiRepository());
    useCase.execute(userId).then(setUser);
  }, [userId]);

  return user;
}
```

## Resources
- Domain entities should have NO framework dependencies
- Use cases orchestrate business logic
- Infrastructure implements interfaces from domain

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
