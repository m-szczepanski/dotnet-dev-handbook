# 13: Raw SQL & Dapper (when EF is not enough)

## What problem does this solve?

Entity Framework can be heavy, slow, and hard to understand for developers who don’t use it every day.  
It forces you to write boilerplate code and can obscure the actual SQL that runs on the database.  
When you need fine‑control over queries—such as dynamic schema changes, non‑relational data stores, or performance‑critical paths—you must bypass EF.  

Dapper is lightweight, fast, and gives you direct control of the raw SQL statements it executes.  
This page shows how to use Dapper for scenarios where EF’s abstraction layer would add unnecessary complexity.

## The Big Idea

Imagine your application as a driver who needs to reach a destination.  
EF is like a GPS that tells you step‑by‑step turns, but every turn takes extra time and can be wrong if the roads change.  
Dapper is like a map you draw yourself: you decide exactly where to go, how long each segment takes, and how many stops to make.  

You still get navigation (the data), but without the overhead of pre‑written routes or unnecessary turns.

## Mechanics Relevant to Developers

### Dapper vs EF

- **EF**: Generates strongly‑typed queries with a mapping layer that hides the raw SQL.
- **Dapper**: Executes a single SQL string and lets you map the result rows manually, giving you full control over column names and data types.

### Dynamic Query Building

- Use `DbCommand` or `SqlConnection` to build your query as a string.
- Pass parameters safely with `@paramName`.
- Execute with `.Execute()` for DML, or `.Query<T>()` for result sets.

### Result Mappings

- Return objects are not strongly typed by Dapper; you must create anonymous types or POCOs that match the SQL columns.
- You can map directly to simple value classes if needed.

## Code Example

```csharp
// Production‑ready example – querying a table that has dynamic columns
static async Task<List<Customer>> GetCustomersAsync(IDbConnection db)
{
    const string sql = @"
        SELECT CustomerId, FirstName, LastName, Email, Phone
        FROM Customers
        WHERE City = @City";

    using var cmd = new DbCommand(sql, db);

    // Safe parameter binding – no SQL injection risk
    cmd.Parameters.Add("@City", DbType.String).Value = "Seattle";
    
    await cmd.ExecuteAsync();

    return await cmd.ReadListAsync<Customer>();
}
```

### Explanation

- The query string is built explicitly so the developer can see exactly what SQL runs on the database.
- Parameters are added safely (`DbCommand` parameters) to avoid injection attacks, a common gotcha when using raw SQL.
- `ReadListAsync<T>()` maps each row to a strongly‑typed `Customer` object that matches the column names in the SQL string.  
  This is a practical choice because it keeps the data shape consistent while still being lightweight.

## Common "Gotchas"

1. **SQL Injection Risk** – Always parameterise queries when using raw SQL; Dapper will not protect you from un‑parameterised strings.
2. **No Strongly‑Typed Result Mapping** – Dapper returns anonymous types or POCOs that must match the query columns exactly; mismatches can cause runtime errors or silent data loss.
3. **Not Using a Connection Pool** – If your code does not use a shared connection, consider reusing `IDbConnection` instances to improve performance.
4. **Ignoring Transaction Scoping** – When using raw SQL in a transaction context, ensure the connection is opened inside a `using` block that matches the transaction scope.

## Opinionated Advice

- **Always parameterise every raw SQL query** you execute with Dapper; trust the framework’s parameter handling and never concatenate user input into the query string.  
- **Prefer strongly‑typed POCOs** for result mappings; if the columns are not strictly consistent, use dynamic types only as a last resort.  
- **Profile memory usage** when executing complex raw queries; Dapper can be faster than EF but may still hold more data in memory.  
- **Consider EF for most CRUD operations** and fall back to Dapper only when you need fine‑grained control or performance tuning.

## When Defaults Are Enough

- **Simple CRUD on relational tables**: If the schema is stable and you just need to insert, update, delete, and read data using standard SQL patterns, the default EF query patterns (e.g., `Add`, `Update`, `Remove`) are more maintainable and less error‑prone.  
- **When using a containerised or managed database**: In environments where the database is abstracted away (e.g., Azure Cosmos DB), you may not need to write raw SQL at all.

## Boundaries of Overkill

- **Overusing Dapper for every query** – If most of your data needs simple CRUD operations, forcing every table into a Dapper‑only path can increase code complexity and reduce readability.  
- **Prematurely writing raw queries for complex business rules**: When the database schema is stable and EF’s fluent API already satisfies the requirements, adding raw SQL may introduce unnecessary maintenance overhead.

## Summary

Dapper is a lightweight, fast alternative to Entity Framework that gives you direct control over the raw SQL statements your application executes.  
It excels in scenarios where you need dynamic query building or fine‑grained performance tuning but can also become overkill when simple CRUD operations are sufficient and EF’s abstraction layer remains clear and maintainable.

### Take-home Value

1. Dapper is best used for raw, parameterised SQL queries that require fine control over the database.  
2. Always bind parameters safely to avoid SQL injection; never concatenate strings into the query.  
3. Map results to POCOs that match the column names exactly, or use dynamic types only when absolutely necessary.  
4. Re‑use `IDbConnection` instances and keep transactions scoped for better performance.  
5. Consider EF for most CRUD operations; fall back to Dapper only when needed.  
6. Avoid over‑engineering by using Dapper sparingly; the default EF patterns are often clearer and more maintainable for simple scenarios.
