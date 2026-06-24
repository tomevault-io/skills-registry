---
name: add-datasource
description: Add data sources (Dataverse tables or Power Platform connectors) to a Code App. Handles both tabular and nontabular connectors, generates service wrappers and React Query hooks. Use when this capability is needed.
metadata:
  author: ramakrishnan24689
---

# Add Data Source to Code App

Add Dataverse tables or Power Platform connectors as data sources to your Code App with automatic service layer and hook generation.

## Reference

This skill uses official patterns from Microsoft Learn (February 2026). See the codeapps-dataverse and codeapps-connector agents for detailed patterns.

## What This Skill Does

1. Identifies data source type (Dataverse table or connector)
2. Runs appropriate `pac code add-data-source` command (PAC CLI 1.46+ for Dataverse, 1.50+ for SharePoint)
3. Verifies generated files in `src/generated/`
4. Creates service layer wrapper with best practices (gen-service)
5. Generates React Query hooks for data operations (gen-hook)
6. Provides usage examples

## Usage

### Add Dataverse Table

```
/add-datasource dataverse account
/add-datasource dataverse contact
/add-datasource dataverse todos
```

### Add Power Platform Connector

```
/add-datasource connector shared_office365users [connection-id]
/add-datasource connector shared_sql [connection-id] [table-name] [dataset]
/add-datasource connector shared_sharepointonline [connection-id] [list-name] [site-url]
```

### Let Claude Invoke Automatically

```
Add the account Dataverse table as a data source
Connect to Office 365 Users
Add SQL Server connection for the Users table
```

## Workflow

### Step 1: Identify Data Source Type

Parse arguments to determine:
- **Dataverse**: First argument is "dataverse"
- **Connector**: First argument is "connector" or an API ID

### Step 2: Dataverse Table Flow

#### 2.1: Add Dataverse Table

```bash
pac code add-data-source -a dataverse -t $ARGUMENTS[1]
```

Examples:
```bash
pac code add-data-source -a dataverse -t account
pac code add-data-source -a dataverse -t todos
```

#### 2.2: Verify Generated Files

```bash
# Check generated model
ls src/generated/models/*$ARGUMENTS[1]*Model.ts

# Check generated service
ls src/generated/services/*$ARGUMENTS[1]*Service.ts
```

#### 2.3: Invoke gen-service Skill

Create service layer wrapper:
```
Use the gen-service skill to create a Dataverse service wrapper for $ARGUMENTS[1]
```

The gen-service skill will create a proper service that:
- ✅ Only sends user-controlled fields (no ownerid, statecode, timestamps)
- ✅ Handles Boolean fields with `as any` type assertion
- ✅ Transforms API responses to domain models
- ✅ Provides user-friendly error messages

#### 2.4: Invoke gen-hook Skill

Create React Query hooks:
```
Use the gen-hook skill to create React Query hooks for $ARGUMENTS[1]
```

The gen-hook skill will create:
- `use<TableName>` - Query hook for fetching records
- `useCreate<TableName>` - Mutation hook for creating records
- `useUpdate<TableName>` - Mutation hook for updating records
- `useDelete<TableName>` - Mutation hook for deleting records

### Step 3: Power Platform Connector Flow

#### 3.1: List Available Connections

Help user discover connections:
```bash
pac connection list
```

Display available connections and ask user to select one (if not provided).

#### 3.2: Identify Connector Type

**Nontabular Connectors** (Office 365 Users, Weather, Translator):
- No tables or datasets required
- Provide methods/functions directly

**Tabular Connectors** (SQL Server, SharePoint, Azure Data Explorer):
- Require table name and dataset
- Provide CRUD operations on tables

#### 3.3: Add Connector Data Source

**For Nontabular Connectors**:
```bash
pac code add-data-source -a $ARGUMENTS[1] -c $ARGUMENTS[2]
```

Example:
```bash
pac code add-data-source -a shared_office365users -c <connection-id>
```

**For Tabular Connectors**:

First, list available datasets and tables:
```bash
# List datasets
pac code list-datasets -a $ARGUMENTS[1] -c $ARGUMENTS[2]

# List tables in dataset
pac code list-tables -a $ARGUMENTS[1] -c $ARGUMENTS[2] -d $ARGUMENTS[4]
```

Then add the data source:
```bash
pac code add-data-source -a $ARGUMENTS[1] -c $ARGUMENTS[2] -t $ARGUMENTS[3] -d $ARGUMENTS[4]
```

Example (SQL Server):
```bash
pac code add-data-source -a shared_sql -c <connection-id> -t dbo.Users -d myDatabase
```

Example (SharePoint):
```bash
pac code add-data-source -a shared_sharepointonline -c <connection-id> -t Tasks -d <site-url>
```

**For SQL Stored Procedures**:
```bash
pac code add-data-source -a shared_sql -c $ARGUMENTS[2] -d $ARGUMENTS[3] -sp $ARGUMENTS[4]
```

#### 3.4: Verify Generated Files

```bash
ls src/generated/models/
ls src/generated/services/
```

#### 3.5: Invoke gen-service Skill

```
Use the gen-service skill to create a connector service wrapper for $ARGUMENTS[1]
```

#### 3.6: Invoke gen-hook Skill

```
Use the gen-hook skill to create React Query hooks for $ARGUMENTS[1]
```

### Step 4: Connection References (ALM Best Practice)

For better ALM, suggest using connection references instead of direct connections:

```bash
# List connection references in solution
pac code list-connection-references -s <solution-id>

# Add with connection reference
pac code add-data-source -a $ARGUMENTS[1] -cr <connection-reference-logical-name> -s <solution-id>
```

**Benefits**:
- ✅ Connections reconfigurable per environment
- ✅ Better for Dev → Test → Prod promotion
- ✅ No code changes when moving between environments

### Step 5: Usage Examples

Provide code examples showing how to use the generated service and hooks:

**Dataverse Example**:
```typescript
// In a component
import { useAccounts, useCreateAccount } from '@/features/accounts/hooks/useAccounts';

function AccountsPage() {
  const { data: accounts, isLoading } = useAccounts();
  const createAccount = useCreateAccount();

  const handleCreate = async (name: string) => {
    await createAccount.mutateAsync({ name });
  };

  if (isLoading) return <Spinner />;

  return (
    <div>
      {accounts?.map(account => (
        <div key={account.id}>{account.name}</div>
      ))}
    </div>
  );
}
```

**Connector Example (Office 365 Users)**:
```typescript
import { useUserProfile } from '@/features/profile/hooks/useUserProfile';

function ProfilePage() {
  const { data: profile, isLoading } = useUserProfile();

  if (isLoading) return <Spinner />;

  return (
    <div>
      <h1>{profile?.displayName}</h1>
      <p>{profile?.jobTitle}</p>
      <p>{profile?.email}</p>
    </div>
  );
}
```

### Step 6: Success Summary

```
✅ Data source added successfully!

📊 Data Source: $ARGUMENTS[1]
📁 Generated Files:
   - src/generated/models/<Name>Model.ts
   - src/generated/services/<Name>Service.ts
   - src/features/<name>/services/<Name>Service.ts
   - src/features/<name>/hooks/use<Name>.ts

🔍 Next Steps:
1. Review generated service wrapper
2. Test data operations: npm run dev
3. Add UI components to display/modify data
4. Deploy when ready: /deploy-codeapp

💡 Pro Tip: Never modify files in src/generated/ - they're auto-generated!
```

## Common Data Source Types

### Officially Tested Connectors

| Connector | API ID | Type | Use Case |
|-----------|--------|------|----------|
| Dataverse | `dataverse` | Tabular | Tables, CRUD operations |
| SQL Server | `shared_sql` | Tabular | Database tables, stored procedures |
| SharePoint | `shared_sharepointonline` | Tabular | Lists, libraries |
| Office 365 Users | `shared_office365users` | Nontabular | User profiles, photos |
| Office 365 Groups | `shared_office365groups` | Nontabular | Group management |
| OneDrive for Business | `shared_onedriveforbusiness` | Nontabular | File operations |
| Microsoft Teams | `shared_teams` | Nontabular | Teams integration |
| MSN Weather | `shared_msnweather` | Nontabular | Weather data |
| Translator | `shared_microsofttranslator` | Nontabular | Translation |

## Error Handling

### Error: Connection not found

**Symptom**: `pac code add-data-source` fails with connection not found

**Fix**:
1. List connections: `pac connection list`
2. If missing, user must create in Power Apps portal:
   - Go to https://make.powerapps.com
   - Navigate to Connections
   - Create new connection
3. Get connection ID and retry

### Error: Table/dataset not found

**Symptom**: Can't find specified table or dataset

**Fix**:
```bash
# List available datasets
pac code list-datasets -a <api-id> -c <connection-id>

# List tables in dataset
pac code list-tables -a <api-id> -c <connection-id> -d <dataset>
```

### Error: Build fails after adding data source

**Symptom**: TypeScript compilation errors after adding data source

**Fix**:
1. Check `power.config.json.backup` for configuration
2. Delete `src/generated/` folder
3. Re-add data source:
   ```bash
   pac code delete-data-source -a <api-id> -ds <data-source-name>
   pac code add-data-source ...
   ```
4. Rebuild: `npm run build`

### Error: Permission denied

**Symptom**: Can't access connector or table

**Fix**:
1. Verify user has permissions to the resource
2. Check connection status in Power Apps portal
3. Re-authenticate if needed:
   ```bash
   pac auth clear
   pac auth create
   ```

## Remove Data Source

If you need to remove a data source:

```bash
# Remove data source
pac code delete-data-source -a <api-id> -ds <data-source-name>

# Remove stored procedure
pac code delete-data-source -a <api-id> -ds <data-source-name> -sp <stored-procedure-name>
```

**Note**: This removes generated files. You'll need to manually delete custom service layers and hooks.

## Validation

After adding data source, verify:
- [ ] Generated model file exists
- [ ] Generated service file exists
- [ ] Custom service wrapper created
- [ ] React Query hooks created
- [ ] TypeScript compiles: `npm run build`
- [ ] No ESLint errors: `npm run lint`
- [ ] Data operations work in local dev: `npm run dev`

## Related Skills

- **Generate service**: `/gen-service` (invoked automatically)
- **Generate hook**: `/gen-hook` (invoked automatically)
- **Deploy app**: `/deploy-codeapp`
- **Fix issues**: `/fix-codeapp-issue`

---

**Pro Tip**: Use connection references for better ALM when deploying across environments. They allow you to reconfigure connections without code changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramakrishnan24689) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
