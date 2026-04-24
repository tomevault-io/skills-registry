---
name: sql-development
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# SQL Development Standards

## Database Schema Generation

- **MUST** use singular form for all table names
- **MUST** use singular form for all column names
- **MUST** include a primary key column named `id` in all tables
- **MUST** include `created_at` column to store creation timestamp
- **MUST** include `updated_at` column to store last update timestamp

## Database Schema Design

- **MUST** define primary key constraint for all tables
- **MUST** name all foreign key constraints
- **MUST** define foreign key constraints inline
- **MUST** use `ON DELETE CASCADE` option for foreign key constraints
- **MUST** use `ON UPDATE CASCADE` option for foreign key constraints
- **MUST** reference the primary key of the parent table in foreign key constraints

## SQL Coding Style

- **MUST** use uppercase for SQL keywords (SELECT, FROM, WHERE)
- **MUST** use consistent indentation for nested queries and conditions
- **MUST** include comments to explain complex logic
- **SHOULD** break long queries into multiple lines for readability
- **MUST** organize clauses consistently (SELECT, FROM, JOIN, WHERE, GROUP BY, HAVING, ORDER BY)

## SQL Query Structure

- **MUST** use explicit column names in SELECT statements instead of `SELECT *`
- **MUST** qualify column names with table name or alias when using multiple tables
- **SHOULD** limit the use of subqueries when joins can be used instead
- **MUST** include LIMIT/TOP clauses to restrict result sets
- **MUST** use appropriate indexing for frequently queried columns
- **MUST NOT** use functions on indexed columns in WHERE clauses

## Stored Procedure Naming Conventions

- **MUST** prefix stored procedure names with `usp_`
- **MUST** use PascalCase for stored procedure names
- **MUST** use descriptive names that indicate purpose (e.g., `usp_GetCustomerOrders`)
- **MUST** include plural noun when returning multiple records (e.g., `usp_GetProducts`)
- **MUST** include singular noun when returning single record (e.g., `usp_GetProduct`)

## Parameter Handling

- **MUST** prefix parameters with `@`
- **MUST** use camelCase for parameter names
- **SHOULD** provide default values for optional parameters
- **MUST** validate parameter values before use
- **MUST** document parameters with comments
- **SHOULD** arrange parameters consistently (required first, optional later)

## Stored Procedure Structure

- **MUST** include header comment block with description, parameters, and return values
- **MUST** return standardized error codes/messages
- **MUST** return result sets with consistent column order
- **SHOULD** use OUTPUT parameters for returning status information
- **MUST** prefix temporary tables with `tmp_`

### Example Stored Procedure

```sql
/*
  Procedure: usp_GetCustomerOrders
  Description: Retrieves all orders for a specific customer
  Parameters:
    @customerId INT - The ID of the customer
    @startDate DATETIME - Optional start date filter
  Returns: Result set of customer orders
*/
CREATE PROCEDURE usp_GetCustomerOrders
    @customerId INT,
    @startDate DATETIME = NULL
AS
BEGIN
    SET NOCOUNT ON;

    -- Validate parameters
    IF @customerId IS NULL OR @customerId <= 0
    BEGIN
        RAISERROR('Invalid customer ID', 16, 1);
        RETURN -1;
    END

    -- Main query
    SELECT
        o.id,
        o.order_date,
        o.total_amount,
        o.status
    FROM
        [order] o
    WHERE
        o.customer_id = @customerId
        AND (@startDate IS NULL OR o.order_date >= @startDate)
    ORDER BY
        o.order_date DESC;

    RETURN 0;
END
```

## SQL Security Best Practices

- **MUST** parameterize all queries to prevent SQL injection
- **MUST** use prepared statements when executing dynamic SQL
- **MUST NOT** embed credentials in SQL scripts
- **MUST** implement proper error handling without exposing system details
- **SHOULD** avoid using dynamic SQL within stored procedures

### SQL Injection Prevention

```sql
-- BAD: Vulnerable to SQL injection
DECLARE @sql NVARCHAR(MAX);
SET @sql = 'SELECT * FROM users WHERE username = ''' + @username + '''';
EXEC(@sql);

-- GOOD: Parameterized query
SELECT * FROM users WHERE username = @username;
```

## Transaction Management

- **MUST** explicitly begin and commit transactions
- **MUST** use appropriate isolation levels based on requirements
- **MUST NOT** create long-running transactions that lock tables
- **SHOULD** use batch processing for large data operations
- **MUST** include `SET NOCOUNT ON` for stored procedures that modify data

### Example Transaction

```sql
BEGIN TRANSACTION;
BEGIN TRY
    -- Your SQL operations here
    UPDATE account SET balance = balance - @amount WHERE id = @fromAccount;
    UPDATE account SET balance = balance + @amount WHERE id = @toAccount;

    COMMIT TRANSACTION;
END TRY
BEGIN CATCH
    IF @@TRANCOUNT > 0
        ROLLBACK TRANSACTION;

    -- Log error without exposing details
    DECLARE @ErrorMessage NVARCHAR(4000) = ERROR_MESSAGE();
    RAISERROR('Transaction failed', 16, 1);
END CATCH
```

## Performance Best Practices

- **MUST** analyze query execution plans for optimization opportunities
- **SHOULD** use covering indexes for frequently executed queries
- **MUST** avoid SELECT * in production code
- **SHOULD** use EXISTS instead of COUNT(*) for existence checks
- **MUST** minimize the use of cursors; use set-based operations instead
- **SHOULD** implement appropriate query hints only when necessary

## Testing and Validation

- **MUST** test stored procedures with various input scenarios
- **MUST** validate edge cases (NULL values, empty strings, boundary conditions)
- **SHOULD** include performance testing for critical queries
- **MUST** verify that error handling works as expected
- **SHOULD** document expected behavior and test results

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
