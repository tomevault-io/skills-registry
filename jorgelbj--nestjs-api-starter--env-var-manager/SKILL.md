---
name: env-var-manager
description: Specialized skill for managing environment variables when implementing new features in NestJS projects. Provides standardized workflow to identify necessary environment variables for a feature and generate proper TypeScript types, Joi validation, and ConfigService getters with Spanish documentation. Use when this capability is needed.
metadata:
  author: jorgelbj
---

# Environment Variables Manager Skill

Use this skill when implementing new features that require environment variables in NestJS projects. This skill guides through identifying necessary variables, validating naming conventions, and generating proper configuration.

## Quick Start

When adding a feature requiring environment variables:

1. Identify the feature type (database, cache, queue, email, storage, auth, etc.)
2. Consult corresponding reference file in `references/` directory
3. Propose all necessary environment variables to user
4. Validate specific configuration options with user (URL format, auth requirements, etc.)
5. Generate code following standard workflow for each variable

## Naming Conventions

All environment variables MUST follow these conventions:

- **Uppercase**: All letters uppercase
- **English names**: Variable names in English only
- **Underscore separator**: Use `_` for multi-word names
- **No special characters**: No accents or special characters
- **Examples**: `DATABASE_URL`, `REDIS_URL`, `API_TIMEOUT`, `JWT_SECRET`

## Standard Workflow

### Step 1: Review Existing Environment Variables

Before adding new environment variables, review existing configuration to prevent duplicates:

1. Check `src/config/env/env.types.ts` for existing variable names
2. Check `src/config/env/env.schema.ts` for existing validation rules
3. Check `.env.example` for documented variables
4. Ensure no duplicate variable names (case-sensitive, uppercase with underscores)

**Project's current environment variables:**

- NODE_ENV (development | production | test)
- PORT (optional, default: 8000)
- LOGGER_LEVEL (optional, default: 'log')

### Step 2: Add Type Definition

Add type definition to `src/config/env/env.types.ts`:

- Add property to `EnvironmentVariables` interface
- Include JSDoc comment in Spanish explaining usage
- Include `@default` if applicable (for optional variables)
- Use `?` for optional properties
- Ensure variable name doesn't conflict with existing variables

### Step 3: Add Validation

Add validation to `src/config/env/env.schema.ts`:

- Add validation rule with Joi
- Use `.required()` for mandatory variables (WITHOUT .default())
- Use `.default(value)` for optional variables (WITH .default())
- Match type definition exactly
- Ensure variable name matches type definition exactly

### Step 4: Add Getter

Add getter to `src/config/app-config.service.ts`:

- Create getter method with camelCase name
- Include JSDoc comment in Spanish with @default if applicable
- Return type from `EnvironmentVariables` interface
- **ALWAYS use `!` assertion** - NEVER use `??` for defaults
- **If variable has .default() in schema**: Type is non-optional (e.g., `string`, `number`) and uses `!`
- **If variable is .required() in schema**: Type is non-optional and uses `!`
- **If variable is .optional() without default**: Type is `string | undefined` and uses NO assertion (neither `!` nor `??`)

### Step 5: Update .env.example

Update `.env.example`:

- Add variable with example value
- Include comments explaining purpose
- Ensure variable name matches definition exactly
- Add after existing variables in alphabetical order if possible

### Step 6: Add Unit Test

Add unit test for each new property in `test/unit/config/app-config.service.spec.ts`:

- Test that getter returns correct value
- Test that getter respects defaults
- Test with and without the variable set in environment

## ⚠️ FILOSOFÍA IMPORTANTE: Defaults Solo en Schema

**REGLA DE ORO: Todos los valores por defecto deben definirse ÚNICAMENTE en `env.schema.ts` usando `.default()`**

**NUNCA uses `??` en los getters del `app-config.service.ts` para aplicar defaults**

**Razones:**

- ✅ Única fuente de verdad (el schema)
- ✅ Más fácil de mantener
- ✅ Evita duplicación de defaults
- ✅ Evita inconsistencias entre schema y service
- ✅ Joi aplica el default automáticamente en tiempo de validación

## Handling Defaults Logic

### Required Variables (Sin Default)

Variables obligatorias que DEBEN estar en `.env`:

```typescript
// env.schema.ts
DATABASE_URL: Joi.string().uri().required()  // Sin .default()

// env.types.ts
/**
 * URL de conexión a la base de datos
 */
DATABASE_URL: string;  // Sin ? porque es obligatorio

// app-config.service.ts
/**
 * Obtiene la URL de conexión a la base de datos
 * @returns URL de conexión
 */
get databaseUrl(): string {
  return this.nestConfigService.get('DATABASE_URL', { infer: true })!;
  //              ^^^ Usa ! porque .required() garantiza que el valor SIEMPRE existe
}
```

**Si no está en `.env`**: La aplicación NO arranca (Joi lanza error en el startup)

### Optional Variables (Con Default)

Variables opcionales con valor por defecto en el schema:

```typescript
// env.schema.ts
PORT: Joi.number().default(8000)  // Con .default()

// env.types.ts
/**
 * Puerto en el que el servidor escuchará peticiones
 * @default 8000
 */
PORT?: number;  // Con ? porque es opcional

// app-config.service.ts
/**
 * Obtiene el puerto en el que el servidor debe escuchar
 * @returns Número de puerto
 * @default 8000
 */
get port(): number {
  return this.nestConfigService.get('PORT', { infer: true })!;
  //              ^^^ Usa ! porque Joi ya aplicó el default del schema
  //              NO necesita ?? porque el schema garantiza el valor
}
```

**Si no está en `.env`**: Joi aplica automáticamente el valor de `.default()` durante la validación

### Truly Optional Variables (Sin Default, Sin Required)

Variables opcionales sin valor por defecto (casos raros, como features opcionales):

```typescript
// env.schema.ts
OPTIONAL_API_KEY: Joi.string().optional()  // Sin .default(), sin .required()

// env.types.ts
/**
 * API Key opcional para servicio externo
 */
OPTIONAL_API_KEY?: string;  // Con ? porque es opcional y puede ser undefined

// app-config.service.ts
/**
 * Obtiene la API Key opcional
 * @returns API Key o undefined si no está configurada
 */
get optionalApiKey(): string | undefined {
  return this.nestConfigService.get('OPTIONAL_API_KEY', { infer: true });
  //              ^^^ Sin ! porque puede ser undefined
  //              Sin ?? porque queremos que sea undefined si no existe
}
```

**Si no está en `.env`**: Retorna `undefined` (sin error, sin default)

## ❌ Errores Comunes a Evitar

1. **NO usar `?` en Joi schemas**

   ```typescript
   PORT?: Joi.number()  // ❌ INCORRECTO - Joi no usa ?
   PORT: Joi.number().default(8000)  // ✅ CORRECTO
   ```

2. **NO usar `??` en getters para defaults**

   ```typescript
   // ❌ INCORRECTO - duplica el default
   get port(): number {
     return this.nestConfigService.get('PORT', { infer: true }) ?? 8000;
   }

   // ✅ CORRECTO - el schema ya tiene el default
   get port(): number {
     return this.nestConfigService.get('PORT', { infer: true })!;
   }
   ```

3. **NO duplicar defaults entre schema y service**

   ```typescript
   // Schema
   PORT: Joi.number().default(8000)

   // Service
   get port(): number {
     return this.nestConfigService.get('PORT', { infer: true }) ?? 3000;  // ❌ CONFLICTO
   }
   ```

4. **NO olvidar el `!` en variables con default**

   ```typescript
   // ❌ INCORRECTO - falta !
   get port(): number {
     return this.nestConfigService.get('PORT', { infer: true });
   }

   // ✅ CORRECTO - con !
   get port(): number {
     return this.nestConfigService.get('PORT', { infer: true })!;
   }
   ```

## Validation Checklist

Before completing environment variable setup:

- Variable name follows uppercase English with underscores
- Type defined in `env/env.types.ts` with Spanish JSDoc
- Validation added to `env/env.schema.ts` with required/default
- Getter created in `app-config.service.ts` with Spanish JSDoc
- `.env.example` updated with example value
- All four files kept in sync
- **Review existing env vars** in env.types.ts, env.schema.ts, and .env.example to ensure no duplicate variable names
- **Unit test added for each new property** in `test/unit/config/app-config.service.spec.ts`
- **NEVER use `??` for defaults** - defaults only in schema

## Feature-Specific References

Consult these reference files for feature-specific guidance:

- **Database**: See `references/database.md` for PostgreSQL, MySQL, MongoDB
- **Cache**: See `references/cache.md` for Redis, Memcached
- **Queue**: See `references/queue.md` for BullMQ, RabbitMQ, AWS SQS, Kafka
- **Email**: See `references/email.md` for SMTP, SendGrid, AWS SES, Mailgun
- **Storage**: See `references/storage.md` for AWS S3, Azure Blob, GCS, MinIO
- **Auth**: See `references/auth.md` for JWT, OAuth, API Keys

Reference files contain:

- Common variables by category (required, optional with default, optional without default)
- Default values and valid options
- Configuration notes and best practices
- Format examples for URLs and connection strings

## Complex Configurations

For configurations requiring enums or additional helper types:

- Create enum file in `src/config/env/` directory
- Add enum to `EnvironmentVariables` interface type
- Reference enum in both `env.types.ts` and `env.schema.ts`

Example: `environment.enum.ts` for NODE_ENV values

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jorgelbj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
