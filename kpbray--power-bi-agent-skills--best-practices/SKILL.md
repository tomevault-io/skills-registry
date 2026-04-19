---
name: best-practices
description: Applies Power BI best practices, BPA rules, and enterprise standards. Use for quality validation, naming conventions, and performance optimization. Use when this capability is needed.
metadata:
  author: kpbray
---

# Best Practices Skill

This skill helps apply Power BI best practices, Best Practice Analyzer (BPA) rules, and enterprise standards to semantic models and reports.

## When to Use This Skill

- Validating model quality against BPA rules
- Applying naming conventions
- Optimizing model performance
- Adding documentation and descriptions
- Enforcing enterprise governance patterns
- Reviewing DAX measures for quality

## Best Practice Analyzer (BPA) Overview

BPA rules are organized into categories:

1. **Performance** - Optimization and efficiency rules
2. **DAX Expressions** - Measure and calculation quality
3. **Formatting** - Naming and organization standards
4. **Maintenance** - Housekeeping and cleanup rules
5. **Error Prevention** - Validation and safety rules

## Quick Validation Checklist

### Model Structure
- [ ] All tables have descriptions
- [ ] Relationships are one-to-many (no bi-directional unless required)
- [ ] Key columns are marked as hidden
- [ ] Date table is marked as date table
- [ ] No unused columns or tables

### DAX Measures
- [ ] All measures have descriptions (/// comments)
- [ ] DIVIDE() used instead of `/` operator
- [ ] Variables used for repeated expressions
- [ ] Measures organized in display folders
- [ ] No implicit measures (summarizations)

### Naming Conventions
- [ ] Tables: PascalCase, singular nouns
- [ ] Columns: PascalCase with spaces allowed
- [ ] Measures: Business-friendly names
- [ ] No special characters or reserved words

### Performance
- [ ] No calculated columns where measures work
- [ ] Integer keys instead of string keys
- [ ] Narrow tables preferred (fewer columns)
- [ ] Appropriate data types used

## Performance Rules

### HIGH IMPACT

#### Avoid bi-directional relationships
Bi-directional relationships cause performance overhead and can create ambiguity.

**Instead of:**
```tmdl
relationship {{guid}}
	fromColumn: TableA.'Key'
	toColumn: TableB.'Key'
	crossFilteringBehavior: bothDirections
```

**Do this:**
```tmdl
relationship {{guid}}
	fromColumn: TableA.'Key'
	toColumn: TableB.'Key'

// Use DAX CROSSFILTER() in measures when needed
measure 'Filtered Value' =
	CALCULATE(
	    [Base Measure],
	    CROSSFILTER(TableA[Key], TableB[Key], Both)
	)
```

#### Use integer keys instead of strings
Integer comparisons are faster than string comparisons.

**Instead of:**
```tmdl
column 'Product Key'
	dataType: string
```

**Do this:**
```tmdl
column 'Product Key'
	dataType: int64
```

#### Minimize calculated columns
Calculated columns consume memory and slow refresh. Use measures when possible.

**Instead of calculated column:**
```tmdl
column 'Profit Margin' =
	DIVIDE(Sales[Profit], Sales[Revenue], 0)
```

**Use a measure:**
```tmdl
measure 'Profit Margin' =
	DIVIDE(SUM(Sales[Profit]), SUM(Sales[Revenue]), 0)
```

### MEDIUM IMPACT

#### Remove unused columns
Unused columns waste memory. Delete columns not used in:
- Relationships
- Measures
- Visuals
- Filters

#### Avoid high cardinality columns
Columns with many unique values increase model size. Consider:
- Grouping/binning high cardinality data
- Moving detail columns to a separate table
- Using calculated columns only when necessary

#### Use appropriate data types
| Data | Recommended Type |
|------|------------------|
| IDs, Keys | `int64` |
| Flags | `boolean` |
| Currency | `decimal` |
| Percentages | `double` or calculated in DAX |
| Dates | `dateTime` |

## DAX Rules

### REQUIRED

#### Use DIVIDE instead of `/`

**Bad:**
```dax
Margin = Sales[Profit] / Sales[Revenue]
```

**Good:**
```dax
Margin = DIVIDE(Sales[Profit], Sales[Revenue], 0)
```

#### Use VAR for repeated expressions

**Bad:**
```dax
Growth % =
(SUM(Sales[Amount]) - CALCULATE(SUM(Sales[Amount]), SAMEPERIODLASTYEAR(Date[Date])))
/ CALCULATE(SUM(Sales[Amount]), SAMEPERIODLASTYEAR(Date[Date]))
```

**Good:**
```dax
Growth % =
VAR CurrentSales = SUM(Sales[Amount])
VAR PriorSales = CALCULATE(SUM(Sales[Amount]), SAMEPERIODLASTYEAR(Date[Date]))
RETURN
    DIVIDE(CurrentSales - PriorSales, PriorSales)
```

#### Add descriptions to measures

```tmdl
/// Calculates total sales revenue
/// Excludes returns and cancellations
measure 'Total Sales' =
	SUM(Sales[Amount])
```

### RECOMMENDED

#### Avoid IFERROR with aggregations
IFERROR masks data quality issues.

**Bad:**
```dax
Total = IFERROR(SUM(Sales[Amount]), 0)
```

**Good:**
```dax
Total = SUM(Sales[Amount])

// Handle blanks explicitly if needed
Total = COALESCE(SUM(Sales[Amount]), 0)
```

#### Use REMOVEFILTERS instead of ALL
REMOVEFILTERS is more explicit about intent.

**Prefer:**
```dax
All Sales = CALCULATE([Total Sales], REMOVEFILTERS(Products))
```

#### Avoid nested CALCULATE
Deeply nested CALCULATE is hard to maintain.

**Bad:**
```dax
Measure =
CALCULATE(
    CALCULATE(
        CALCULATE([Base], Filter1),
        Filter2
    ),
    Filter3
)
```

**Good:**
```dax
Measure =
CALCULATE(
    [Base],
    Filter1,
    Filter2,
    Filter3
)
```

## Formatting Rules

### Table Naming

| Pattern | Example | Use |
|---------|---------|-----|
| PascalCase | `SalesOrders` | Standard tables |
| Singular | `Customer` not `Customers` | Dimension tables |
| Prefix dim/fact | `dimCustomer`, `factSales` | Optional, for clarity |

### Column Naming

| Pattern | Example |
|---------|---------|
| PascalCase with spaces | `Customer Name` |
| Keys end with Key | `Product Key` |
| IDs end with ID | `Transaction ID` |
| Dates end with Date | `Order Date` |

### Measure Naming

| Category | Pattern | Example |
|----------|---------|---------|
| Aggregations | Noun phrase | `Total Sales` |
| Percentages | End with `%` | `Gross Margin %` |
| Ratios | Include units | `Sales per Customer` |
| Time Intelligence | Include period | `Sales YTD`, `Sales PY` |
| Counts | End with `Count` | `Customer Count` |

### Display Folders

Organize measures into logical folders:

```
├── Core Metrics
│   ├── Total Sales
│   ├── Total Cost
│   └── Total Profit
├── Time Intelligence
│   ├── Sales YTD
│   ├── Sales PY
│   └── Sales YoY %
├── Percentages
│   ├── Gross Margin %
│   └── % of Total
└── KPIs
    ├── Target
    └── Achievement
```

## Maintenance Rules

### Remove unused objects

Delete these if not used:
- Tables with no relationships or references
- Columns not in visuals, measures, or relationships
- Measures not in visuals or other measures
- Hidden columns not used in calculations

### Document everything

Every object should have a description:

```tmdl
/// Customer dimension table
/// Contains customer demographics and segmentation
table Customers
	lineageTag: ...

	/// Unique identifier for each customer
	column 'Customer ID'
		...

	/// Customer's full name (First + Last)
	column 'Customer Name'
		...
```

### Use consistent formatting

- TMDL: Use tabs for indentation
- DAX: Use 4 spaces for indentation
- Follow SQLBI DAX formatting conventions

## Error Prevention Rules

### Validate relationships

Before creating relationships, verify:
1. "To" column contains unique values
2. Data types match exactly
3. Orphan handling is considered
4. Filter direction is appropriate

### Validate measure references

Before using measures in visuals:
1. Test with simple filters
2. Verify time intelligence with date table
3. Check for circular references
4. Validate with EVALUATE in DAX Studio

### Test edge cases

Consider these scenarios:
- Empty filter context (grand total)
- Single value context (card visual)
- Multiple selections
- No data (blanks)

## Enterprise Governance Patterns

### Row-Level Security (RLS)

Define RLS roles in TMDL:

```tmdl
role SalesRep
	modelPermission: read

	tablePermission Sales = 'Sales'[Sales Rep] = USERPRINCIPALNAME()
```

### Data Sensitivity

Mark sensitive columns:

```tmdl
column 'SSN'
	dataType: string
	isHidden
	// Consider not including in model
```

### Audit Trail

Document model changes:
- Use Git commit messages
- Add change log in model description
- Track measure versions with comments

## Data Source Governance

### Credential Security

**NEVER store credentials in source files:**

```tmdl
/// BAD: Hardcoded connection string
expression ConnectionString = "Server=prod.database.com;User=admin;Password=secret123"

/// GOOD: Use parameters without credentials
expression ServerName = "prod.database.com" meta [IsParameterQuery=true, Type="Text"]
```

Credentials should be:
- Configured in Power BI Service after publish
- Managed via gateway for on-premises sources
- Stored in Azure Key Vault for automated scenarios

### Connection Parameters

Use parameters for environment-specific values:

```tmdl
/// Server name parameter (update per environment)
expression ServerName = "dev-server.database.windows.net" meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]

/// Database name parameter
expression DatabaseName = "SalesDB_Dev" meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]
```

### Gateway Configuration

For on-premises or VNet data sources:

| Source Location | Gateway Required |
|-----------------|------------------|
| Cloud (Azure SQL, SharePoint Online) | No |
| On-premises (SQL Server, file share) | Yes |
| VNet (private endpoints) | Yes (VNet gateway) |
| Local files (CSV, Excel) | Yes or use cloud storage |

### Refresh Considerations

**Import Mode:**
- Data copied into model at refresh
- Schedule refreshes in Power BI Service
- Consider incremental refresh for large tables

**DirectQuery:**
- Live queries to source
- No refresh needed
- Source must be always available
- Performance dependent on source

**Hybrid (Composite):**
- Mix of Import and DirectQuery
- Use Import for dimensions, DirectQuery for facts
- Requires careful relationship configuration

### Incremental Refresh Setup

1. Create required parameters in Power Query:

```m
RangeStart = #datetime(2020, 1, 1, 0, 0, 0) meta [IsParameterQuery=true, Type="DateTime"]
RangeEnd = #datetime(2025, 12, 31, 23, 59, 59) meta [IsParameterQuery=true, Type="DateTime"]
```

2. Filter query with these parameters:

```m
Table.SelectRows(Source, each [Date] >= RangeStart and [Date] < RangeEnd)
```

3. Configure refresh policy in Power BI Desktop before publish

### Data Source Checklist

- [ ] No credentials in source files
- [ ] Connection strings use parameters
- [ ] Gateway configured for on-premises sources
- [ ] Query folding verified for large tables
- [ ] Incremental refresh configured where appropriate
- [ ] Timeout settings appropriate for data volume
- [ ] Error handling in Power Query transformations
- [ ] Data source documented in model description

### Query Folding Validation

Ensure transformations push to source database:

| Foldable (DO) | Non-Foldable (AVOID) |
|---------------|---------------------|
| `Table.SelectRows` (simple) | `Table.AddColumn` (custom) |
| `Table.SelectColumns` | `Table.Buffer` |
| `Table.Sort` | Custom functions |
| `Table.Group` | Cross-source joins |

Check folding: Right-click step > "View Native Query"

## Validation Workflow

1. **After creating tables**
   - Verify naming conventions
   - Check data types
   - Add descriptions

2. **After adding relationships**
   - Confirm cardinality
   - Verify no bi-directional unless needed
   - Check filter flow

3. **After writing measures**
   - Run BPA checks
   - Verify DIVIDE usage
   - Add descriptions
   - Test calculations

4. **Before deployment**
   - Remove unused objects
   - Verify all descriptions
   - Check performance
   - Test with sample data

## Integration with Other Skills

After using other skills, apply best practices:

| Skill | Validation Focus |
|-------|------------------|
| `pbip-project` | File structure, encoding |
| `semantic-model` | Relationships, data types |
| `dax` | Measure quality, formatting |
| `report-visuals` | Visual references, performance |
| `power-query` | Query folding, data source parameters |
| `themes` | Accessibility, color contrast |
| `calculation-groups` | Appropriate use, format strings |
| `security` | RLS filter efficiency, complete coverage |
| `deployment` | BPA in CI/CD, secret management |

## BPA Rule Reference

See [bpa-rules.md](references/bpa-rules.md) for complete rule list.

## Naming Convention Reference

See [naming-conventions.md](references/naming-conventions.md) for detailed naming guidelines.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kpbray) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
