---
name: backend-database
description: Repository pattern, transactions, caching ve query optimization. Use when this capability is needed.
metadata:
  author: vuralserhat86
---

# 🗄️ Backend Database

> Database patterns, caching ve performance optimization.

---

## 📋 1. Repository Pattern

```typescript
interface IUserRepository {
  findById(id: string): Promise<User | null>;
  findByEmail(email: string): Promise<User | null>;
  create(data: CreateUserDto): Promise<User>;
  update(id: string, data: UpdateUserDto): Promise<User>;
  delete(id: string): Promise<void>;
}

class UserRepository implements IUserRepository {
  constructor(private prisma: PrismaClient) {}

  async findById(id: string) {
    return this.prisma.user.findUnique({ where: { id } });
  }
}
```

---

## 🔄 2. Transactions

```typescript
async function transferMoney(fromId, toId, amount) {
  return prisma.$transaction(async (tx) => {
    const from = await tx.account.update({
      where: { id: fromId },
      data: { balance: { decrement: amount } },
    });
    
    if (from.balance < 0) throw new Error('Insufficient funds');
    
    await tx.account.update({
      where: { id: toId },
      data: { balance: { increment: amount } },
    });
  });
}
```

---

## ⚡ 3. Caching (Redis)

```typescript
async function getCachedUser(id: string) {
  const cacheKey = `user:${id}`;
  
  const cached = await redis.get(cacheKey);
  if (cached) return JSON.parse(cached);
  
  const user = await userRepository.findById(id);
  if (user) {
    await redis.set(cacheKey, JSON.stringify(user), 'EX', 3600);
  }
  return user;
}
```

---

## 🔍 4. Query Optimization

```typescript
// ❌ N+1 problem
const users = await prisma.user.findMany();
for (const user of users) {
  await prisma.post.findMany({ where: { authorId: user.id } });
}

// ✅ Include ile tek sorgu
const users = await prisma.user.findMany({
  include: { posts: true },
});

// ✅ Select ile sadece gerekli alanlar
const users = await prisma.user.findMany({
  select: { id: true, name: true, email: true },
});
```

---

## ⏱️ 5. Async Best Practices

```typescript
// ❌ Sequential
const user = await getUser(id);
const orders = await getOrders(id);

// ✅ Parallel
const [user, orders] = await Promise.all([
  getUser(id),
  getOrders(id),
]);
```

---

## 🔗 İlgili Skill'ler
- `backend-core` - Yapı, TypeScript
- `backend-api` - Endpoints, response

---

- `backend-api` - Endpoints, response

---

*Backend Database v1.2 - Verified*

## 🔄 Workflow

> **Kaynak:** [12 Factor App - Backing Services](https://12factor.net/backing-services)

### Aşama 1: Schema & Migration
- [ ] **Design**: ER diyagramını çiz ve normalize et.
- [ ] **Migration Tool**: Drizzle Kit, Prisma Migrate veya TypeORM ile versiyonlu migration yapısı kur.
- [ ] **Seed**: Test ve geliştirme için idempotent seed scriptleri yaz.

### Aşama 2: Access Layer (Repository Pattern)
- [ ] **Abstraction**: Veritabanı sorgularını Controller'dan ayır (Repo/DAO).
- [ ] **Injection**: DB instance'ını dependency injection ile servislere ver.
- [ ] **Transactions**: Kritik işlemleri (Para transferi vb.) transaction bloğuna al.

### Aşama 3: Optimization & Safety
- [ ] **Indices**: Yavaş sorguları `EXPLAIN` ile analiz et ve index ekle.
- [ ] **Connection Pooling**: Prod ortamında pool size ayarlarını yap.
- [ ] **Sanitization**: SQL Injection koruması (ORM kullan veya parametreli sorgu yap).

### Kontrol Noktaları
| Aşama | Doğrulama |
|-------|-----------|
| 1 | Migration dosyaları Git'e commit edilmiş mi? |
| 2 | N+1 sorgu problemi var mı? (Loop içinde query) |
| 3 | DB şifresi kodun içinde hardcoded mı? (Asla olmamalı) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vuralserhat86) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
