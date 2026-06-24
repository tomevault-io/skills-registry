---
name: gen-service
description: Generate service layer wrapper for Dataverse tables or Power Platform connectors. Creates type-safe services that wrap generated APIs with business logic, error handling, and data transformations. Use when this capability is needed.
metadata:
  author: ramakrishnan24689
---

# Generate Service Layer Wrapper

Generate a service layer that wraps generated Dataverse or connector services with best practices, error handling, and data transformations.

## What This Skill Does

1. Reads generated model and service files from `src/generated/`
2. Creates domain model types (DTO interfaces)
3. Generates service class with CRUD operations
4. Implements proper error handling and user-friendly messages
5. Handles CodeApps-specific issues (system-managed fields, Boolean type mismatches)
6. Provides data transformation methods (API → domain model)

## Usage

```
/gen-service dataverse account
/gen-service dataverse todos
/gen-service connector office365users
```

## Service Layer Pattern

### For Dataverse Tables

**Generated Service** (`src/generated/services/AccountsService.ts`):
- Low-level API calls
- Auto-generated types
- No business logic
- **DO NOT MODIFY** (regenerated when schema changes)

**Custom Service** (`src/features/accounts/services/AccountService.ts`):
- Wraps generated service
- Business logic and validation
- User-friendly error messages
- Data transformations
- **Safe to modify** (your code)

### For Connectors

Similar pattern but adapted for connector-specific operations (methods instead of CRUD).

## Templates

This skill uses production-ready templates that demonstrate all best practices:

- **templates/dataverse-service.template.ts** - Complete Dataverse service with CRUD operations, Context API integration
- **templates/connector-service.template.ts** - Connector service wrapper pattern
- **examples/todo-service-example.ts** - Complete CRUD example with Dataverse
- **examples/context-usage-example.ts** - Context API patterns (user filtering, environment detection)
- **examples/metadata-usage-example.ts** - getMetadata() patterns (dynamic forms, localized labels)
- **examples/sharepoint-operations-example.ts** - SharePoint-specific patterns (expanded objects, getReferencedEntity)

**How to use templates**: Read the template file for the pattern, then generate adapted code based on the actual generated files you discover.

## Workflow

### Step 1: Identify Data Source Type

Determine if this is:
- **Dataverse table**: CRUD operations → Use dataverse-service template
- **Connector**: Custom methods → Use connector-service template

### Step 2: Read Generated Files

```bash
# Find generated model
ls src/generated/models/*$ARGUMENTS[1]*Model.ts

# Find generated service
ls src/generated/services/*$ARGUMENTS[1]*Service.ts
```

Read both files to understand:
- Available fields and their types
- Boolean fields (will need `as any` type assertion)
- System-managed fields (for Dataverse - must exclude these)
- Available methods (for connectors)

### Step 3: Read the Appropriate Template

**For Dataverse tables:**
```
Read templates/dataverse-service.template.ts
```

**For connectors:**
```
Read templates/connector-service.template.ts
```

Study the template structure, patterns, and TODO comments. This template contains all the best practices you need to follow.

### Step 4: Analyze Fields (Dataverse Only)

Based on the generated model file and the template, identify field categories:

1. **User-controlled fields**: Can be sent in create/update
   - Custom fields (e.g., `title`, `description`, `priority`)
   - Standard editable fields (e.g., `name`, `email`)

2. **System-managed fields** (NEVER send these):
   - Ownership: `ownerid`, `owneridtype`, `owninguser`, `owningteam`
   - State: `statecode`, `statuscode`
   - Timestamps: `createdon`, `modifiedon`, `createdby`, `modifiedby`
   - Metadata: `versionnumber`
   - Primary key on CREATE: `<table>id` (e.g., `accountid`, `todoid`)

3. **Boolean fields** (special handling):
   - Generated as numeric types (0|1) but require boolean (true/false)
   - **CRITICAL**: Use `as any` type assertion when setting (see template)

### Step 5: Create Feature Directory Structure

```bash
# Determine feature name (singular, lowercase)
FEATURE_NAME=$(echo $ARGUMENTS[1] | tr '[:upper:]' '[:lower:]' | sed 's/s$//')

# Create directories
mkdir -p src/features/$FEATURE_NAME/{services,models,hooks,components}
```

### Step 6: Generate Service Layer Following Template Pattern

Now generate the service layer files following the patterns from the template you read:

#### 6.1: Create Domain Models

Create `src/features/<feature>/models/<Name>.ts` following the interface structure from the template:

- Domain model interface (what your app uses)
- CreateDto interface (only user-controlled fields)
- UpdateDto interface (optional user-controlled fields)
- Filters interface (optional query filters)

**Reference the template's TODO comments** for guidance on what fields to include.

#### 6.2: Create Service Class

Create `src/features/<feature>/services/<Name>Service.ts` following the template pattern:

**CRITICAL PATTERNS FROM TEMPLATE (Must Follow)**:

**Key Methods to Implement** (as shown in template):

1. **get<Plural>()**: Fetch multiple records with optional filters
2. **get<Name>ById(id)**: Fetch single record
3. **create<Name>(dto)**: Create new record (ONLY user-controlled fields)
4. **update<Name>(id, dto)**: Update existing record (ONLY changed fields)
5. **delete<Name>(id)**: Delete record
6. **toDomainModel()**: Private method to transform API response → domain model
7. **buildFilter()**: Private method to build OData filter strings
8. **validateCreateDto()**: Private method for input validation

**Critical Pattern #1: CREATE with System-Managed Field Exclusion**

The template shows this pattern - adapt it to your actual fields:

```typescript
static async create<Name>(dto: Create<Name>Dto): Promise<<Name>> {
  try {
    this.validateCreateDto(dto);

    // ✅ ONLY user-controlled fields
    const payload = {
      // Map DTO fields to Dataverse fields
      fieldname: dto.fieldValue.trim(),
      booleanfield: dto.boolValue as any, // ✅ Boolean → use 'as any'
      datefield: dto.dateValue?.toISOString(),
      // ❌ NO ownerid, statecode, timestamps, primary key
    };

    const result = await GeneratedService.create(
      payload as Parameters<typeof GeneratedService.create>[0]
    );

    return this.toDomainModel(result.data);
  } catch (error) {
    console.error('Failed to create:', error);
    throw new Error('Unable to create record. Please try again.');
  }
}
```

**Critical Pattern #2: UPDATE with Conditional Field Updates**

```typescript
static async update<Name>(id: string, dto: Update<Name>Dto): Promise<void> {
  try {
    const updates: Partial<any> = {};

    // Only include fields that are being changed
    if (dto.field !== undefined) {
      updates.dataversefield = dto.field;
    }
    if (dto.boolField !== undefined) {
      updates.dataverseboolfield = dto.boolField as any; // ✅ Boolean → 'as any'
    }

    if (Object.keys(updates).length === 0) {
      return; // No changes to apply
    }

    await GeneratedService.update(id, updates);
  } catch (error) {
    console.error('Failed to update:', error);
    throw new Error('Unable to update record. Please try again.');
  }
}
```

**Critical Pattern #3: Data Transformation**

```typescript
private static toDomainModel(record: any): <Name> {
  return {
    id: record.<primarykey>,
    fieldName: record.dataversefield,
    boolField: !!record.dataverseboolfield, // ✅ Force boolean
    dateField: record.datefield ? new Date(record.datefield) : null,
    createdOn: new Date(record.createdon),
    modifiedOn: new Date(record.modifiedon),
  };
}

// Transform API response to domain model
private static toModel(record: any): Todo {
  return {
    id: record.todoid,
    title: record.title,
    description: record.description,
    isCompleted: !!record.iscompleted, // Force boolean
    priority: record.priority,
    dueDate: record.duedate ? new Date(record.duedate) : null,
    createdOn: new Date(record.createdon),
    modifiedOn: new Date(record.modifiedon),
  };
}
```

#### 6.3: Verify Against Template Checklist

Before completing, verify your generated service includes:

- ✅ Imports from generated service and models
- ✅ Domain model interfaces (not just generated types)
- ✅ CreateDto with ONLY user-controlled fields
- ✅ UpdateDto with optional user-controlled fields
- ✅ All methods with try-catch error handling
- ✅ User-friendly error messages (not raw errors)
- ✅ Boolean fields use `as any` type assertion
- ✅ NO system-managed fields in create/update payloads
- ✅ Data transformation method (toDomainModel)
- ✅ JSDoc comments on public methods

**Run validation script** (if available):
```bash
./skills/gen-service/scripts/validate-service.sh src/features/<feature>/services/<Name>Service.ts
```

### Step 7: Generate Service Class (Connector)

For connectors, follow the connector template pattern:

For connectors, adapt the template based on connector type:

**Nontabular Connector** (Office 365 Users):
```typescript
export class UserProfileService {
  static async getCurrentUserProfile(): Promise<UserProfile> {
    try {
      const profile = (await Office365UsersService.MyProfile_V2(
        "id,displayName,jobTitle,mail,userPrincipalName"
      )).data;

      return {
        id: profile.id,
        displayName: profile.displayName,
        jobTitle: profile.jobTitle,
        email: profile.mail,
        username: profile.userPrincipalName,
      };
    } catch (error) {
      console.error('Failed to fetch user profile:', error);
      throw new Error('Unable to load your profile. Please try again.');
    }
  }

  static async getUserPhoto(userId: string): Promise<string | null> {
    try {
      const photoData = (await Office365UsersService.UserPhoto_V2(userId)).data;
      return `data:image/jpeg;base64,${photoData}`;
    } catch (error) {
      console.warn('Photo not available for user:', userId);
      return null; // Graceful degradation
    }
  }
}
```

**Tabular Connector** (SQL, SharePoint):
Similar to Dataverse pattern but with connector-specific field names.

### Step 8: Add JSDoc Documentation

Add comprehensive JSDoc comments to all public methods:

```typescript
/**
 * Get all active todos with optional filtering
 * @param filters Optional filters to apply
 * @returns Promise resolving to array of todos
 * @throws Error if fetch fails or user lacks permissions
 */
static async getTodos(filters?: TodoFilters): Promise<Todo[]> {
  // ...
}
```

### Step 9: Validate Generated Service

Check that the service:
- [ ] Only sends user-controlled fields in create/update
- [ ] Handles Boolean fields with `as any`
- [ ] Provides user-friendly error messages
- [ ] Transforms API responses to domain models
- [ ] Includes JSDoc documentation
- [ ] TypeScript compiles without errors

```bash
# Compile check
npm run build

# Lint check
npm run lint
```

## Error Handling Patterns

### User-Friendly Error Messages

```typescript
try {
  // Operation
} catch (error) {
  console.error('Failed to create todo:', error);
  throw new Error('Unable to create todo. Please try again.');
}
```

### Graceful Degradation

For non-critical operations (like user photos):
```typescript
try {
  return await getPhoto(userId);
} catch (error) {
  console.warn('Photo not available:', error);
  return null; // Don't fail the entire operation
}
```

## Output Summary

```
✅ Service layer generated successfully!

📁 Created Files:
   - src/features/<feature>/models/<Name>.ts
   - src/features/<feature>/services/<Name>Service.ts

🔍 Service Methods:
   - get<Name>s(): Fetch multiple records
   - get<Name>ById(id): Fetch single record
   - create<Name>(dto): Create new record
   - update<Name>(id, dto): Update existing record
   - delete<Name>(id): Delete record

⚠️ Important:
   - Service only sends user-controlled fields (no ownerid, statecode)
   - Boolean fields use 'as any' type assertion
   - Errors provide user-friendly messages
   - API responses transformed to domain models

💡 Next Steps:
1. Review generated service
2. Create React Query hooks: /gen-hook $ARGUMENTS[0] $ARGUMENTS[1]
3. Add UI components
4. Test: npm run dev

📚 Files to NEVER modify:
   - src/generated/models/*
   - src/generated/services/*
```

## Common Issues

### Issue: Type errors in generated files

**Cause**: PAC CLI generated incorrect types

**Solution**: DO NOT modify generated files. Wrap with service and use type assertions.

### Issue: Boolean field errors

**Symptom**: `Cannot convert literal '1' to expected type 'Edm.Boolean'`

**Solution**: Always use `as any` when setting Boolean fields:
```typescript
updates.booleanField = value as any;
```

### Issue: System-managed field errors

**Symptom**: `PrimitiveValue node with non-null value... 'ownerid'`

**Solution**: Remove all system-managed fields from payload.

## Related Skills

- **Generate hooks**: `/gen-hook` (create React Query hooks for this service)
- **Add data source**: `/add-datasource` (adds Dataverse table/connector)
- **Generate component**: `/gen-component` (create UI for this service)

---

**Pro Tip**: Always wrap generated services with custom services. Never use generated services directly in your components.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramakrishnan24689) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
