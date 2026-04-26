---
name: generate-test-data
description: Generate realistic test data with Bogus or Faker.js Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# Generate Test Data

Generate realistic test data matching your domain models.

## For .NET (Bogus 9.6K stars)
```bash
dotnet add package Bogus
```
```csharp
var faker = new Faker<CandidateProfile>("fr")
    .RuleFor(c => c.Id, f => Guid.NewGuid().ToString())
    .RuleFor(c => c.FirstName, f => f.Name.FirstName())
    .RuleFor(c => c.LastName, f => f.Name.LastName())
    .RuleFor(c => c.Email, (f, c) => f.Internet.Email(c.FirstName, c.LastName))
    .RuleFor(c => c.Skills, f => f.Make(3, () => f.PickRandom(skills)));
var candidates = faker.Generate(100);
```

## For TypeScript (Faker.js 15K stars)
```typescript
import { faker } from '@faker-js/faker/locale/fr';
const candidates = Array.from({ length: 100 }, () => ({
  firstName: faker.person.firstName(),
  lastName: faker.person.lastName(),
  email: faker.internet.email(),
  skills: faker.helpers.arrayElements(['CSharp', 'Azure', 'React'], 3),
}));
```

## Arguments
- `<entity-name>`: Domain entity to generate data for
- `--count=<n>`: Number of records (default: 100)
- `--locale=<code>`: Locale for names/addresses (default: fr)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
