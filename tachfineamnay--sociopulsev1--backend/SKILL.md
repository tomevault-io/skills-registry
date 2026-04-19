---
name: backend
description: name: Backend Architecture Expert Use when this capability is needed.
metadata:
  author: tachfineamnay
---
```skill
---
name: Backend Architecture Expert
description: NestJS 10 modular API — 20 feature modules, guards, decorators, WebSocket, Stripe, rate limiting, CORS.
version: 2.1.0
updated: 2026-02-28
---

# Backend Architecture — SocioPulse V2

> Verified against `apps/api/src/` source, `main.ts`, and `app.module.ts` (2026-02-28).

---

## 1. Bootstrap (`main.ts`)

```typescript
// Global prefix
app.setGlobalPrefix('api/v1');

// Swagger docs at /docs
SwaggerModule.setup('docs', app, document);
// Title: "Les Extras V2 API" (⚠️ legacy name in Swagger, app is SocioPulse)

// Validation (global)
app.useGlobalPipes(new ValidationPipe({
  whitelist: true,
  forbidNonWhitelisted: true,
  transform: true,
}));
```

**Port:** 4000  
**Swagger:** `http://localhost:4000/docs`  
**Health:** `GET /api/health` (via HealthModule)

---

## 2. Module Structure

### Feature Modules (20)

```
apps/api/src/
├── auth/              # JWT register, login, me, password reset
├── admin/             # User management, stats, moderation
├── matching-engine/   # Talent↔Mission matching (Haversine + scoring)
├── video-booking/     # LiveKit session management
├── wall-feed/         # Fil Pro / Communauté feed
├── contracts/         # Devis → Contract e-signature flow
├── messages/          # Chat (Socket.IO rooms per mission)
├── payments/          # Stripe Connect (intents, webhooks, payouts)
├── health/            # Healthcheck endpoint
├── growth/            # Gamification (points, referrals)
├── mission-hub/       # Mission tracking (timeline, reports, instructions)
├── notifications/     # Push + WebSocket notifications
├── availability/      # Talent availability slots
├── finance/           # Wallet, transactions, invoices
├── talents/           # Talent CRUD, profile, compliance
├── quotes/            # Quote management (DRAFT→ACCEPTED)
├── dashboard/         # Dashboard stats aggregation
├── pulse/             # Pulse Economy (wallet, transactions, packs)
├── support/           # Support tickets management
└── disputes/          # Booking dispute resolution
```

### Common Modules (7 directories)

```
apps/api/src/common/
├── guards/            # jwt-auth, roles, mission-access, seed-interception
├── decorators/        # current-user, roles, seed-action
├── filters/           # http-exception filter
├── prisma/            # PrismaModule (DB access)
├── uploads/           # File upload handling
├── mailer/            # SMTP/Resend email
└── services/          # Geocoding (Haversine)
```

---

## 3. Guards

| Guard | Location | Purpose |
|-------|----------|---------|
| `JwtAuthGuard` | `common/guards/` | Validates Bearer JWT token |
| `RolesGuard` | `common/guards/` | Enforces `@Roles()` decorator |
| `MissionAccessGuard` | `common/guards/` | Mission-scoped data access control |
| `SeedInterceptionGuard` | `common/guards/` | Blocks real actions on seed/demo data (`isSeed: true`) |
| `PulseGuard` | `pulse/pulse.guard.ts` | ⚠️ NOT in common — checks Pulse wallet balance before actions |

### Usage Pattern

```typescript
@UseGuards(JwtAuthGuard, RolesGuard)  // ⚠️ Order matters! JWT first, then Roles
@Roles('CLIENT')                       // Must come AFTER @UseGuards
@Post('missions')
async create(@CurrentUser() user: CurrentUserPayload) {
  // user.id, user.email, user.role, user.status
}
```

---

## 4. Decorators

| Decorator | Location | Purpose |
|-----------|----------|---------|
| `@CurrentUser()` | `common/decorators/` | Extracts JWT payload → `CurrentUserPayload` |
| `@Roles('CLIENT')` | `common/decorators/` | Sets required role metadata for `RolesGuard` |
| `@SeedAction()` | `common/decorators/` | Marks endpoints that `SeedInterceptionGuard` should check |

---

## 5. Rate Limiting (ThrottlerModule)

```typescript
// app.module.ts — 3 tiers, applied globally
ThrottlerModule.forRoot([
  { name: 'short',  ttl: 1000,  limit: 10  },   // 10 req/1s
  { name: 'medium', ttl: 10000, limit: 50  },   // 50 req/10s
  { name: 'long',   ttl: 60000, limit: 200 },   // 200 req/60s
])

// Global guard
providers: [{ provide: APP_GUARD, useClass: ThrottlerGuard }]
```

---

## 6. CORS Configuration

```typescript
// main.ts — static origins + dynamic from env
const staticOrigins = [
  'http://localhost:3000', 'http://localhost:3001',
  'https://sociopulse.fr', 'https://www.sociopulse.fr', 'https://dash.sociopulse.fr',
  'https://medicopulse.fr', 'https://www.medicopulse.fr',
  'https://api.sociopulse.fr',
];
// + FRONTEND_URL env var (comma-separated)
// + regex: localhost:* and *.sslip.io for dev
```

---

## 7. DTOs & Validation

```typescript
import { IsEmail, IsString, MinLength, IsEnum } from 'class-validator';

export class RegisterDto {
  @IsEmail()
  email: string;

  @IsString()
  @MinLength(8)
  password: string;

  @IsEnum(UserRole)
  role: UserRole;
}
```

Global `ValidationPipe` with `whitelist: true` strips unknown fields.

---

## 8. Matching Engine

```typescript
// matching-engine/matching-engine.service.ts
async findMatches(missionId: string): Promise<Profile[]> {
  // 1. Geo filter (Haversine distance → profile.radiusKm)
  // 2. Job filter (exact jobId match)
  // 3. Compliance check (diploma, driver license, night shift)
  // 4. Specialty scoring (0-100 — overlap between talent.specialties & mission.specialtiesTags)
  // 5. Sort by score + averageRating → return top 3
}
```

---

## 9. WebSocket (Socket.IO)

```typescript
// messages/messages.gateway.ts
@WebSocketGateway({ cors: { origin: '*' } })
export class MessagesGateway {
  @SubscribeMessage('chat:send')
  handleMessage(@MessageBody() data, @ConnectedSocket() client) {
    // Persist + broadcast to mission room
    this.server.to(`mission-${data.missionId}`).emit('chat:message', message);
  }

  @SubscribeMessage('mission:join')
  joinRoom(@MessageBody() missionId, @ConnectedSocket() client) {
    client.join(`mission-${missionId}`);
  }
}
```

Events: `chat:send`, `chat:message`, `mission:join`, `mission:new`, `notification`

---

## 10. Stripe Integration

```typescript
// payments/payments.service.ts
// Stripe Connect marketplace mode

// PaymentIntent creation
await stripe.paymentIntents.create({
  amount: amount * 100,  // cents
  currency: 'eur',
  customer: customerId,
});

// Webhook handling
@Post('webhooks')
handleWebhook(@Req() req) {
  const event = stripe.webhooks.constructEvent(…);
  // payment_intent.succeeded → processPaymentSuccess()
}
```

---

## 11. Error Handling

```typescript
// common/filters/http-exception.filter.ts
@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception, host) {
    response.status(status).json({
      statusCode: status,
      message: exception.message,
      timestamp: new Date().toISOString(),
    });
  }
}
```

---

## 12. Static Assets

Backend serves uploaded files from:
- `public/invoices/`
- `uploads/avatars/`
- `uploads/documents/`
- `uploads/services/`
- `uploads/chat/`

---

## 13. Rules

### ✅ DO
- One feature = one module
- Use DTOs for ALL inputs (validation)
- Always `@UseGuards(JwtAuthGuard, RolesGuard)` then `@Roles()`
- Business logic in Services, not Controllers
- Handle WebSocket errors (disconnection, room cleanup)

### ❌ DON'T
- Put business logic in controllers
- Skip validation (always use DTOs)
- Hardcode secrets (use `ConfigModule` / env vars)
- Forget rate limiting exists (3-tier: 10/1s, 50/10s, 200/60s)
- Place new guards in `pulse/` — put them in `common/guards/` unless pulse-specific

```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tachfineamnay) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
