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
- **Dapper**: Executes a SQL string and maps result rows to your types (usually POCOs). The mapping is convention‑based and mismatches show up at runtime.

### Dynamic Query Building

- For raw ADO.NET, create commands via `DbConnection.CreateCommand()` (because `DbCommand` is abstract).
- For Dapper, call `QueryAsync<T>` / `ExecuteAsync` directly on an `IDbConnection`.
- Pass parameters safely with `@paramName`.
- Execute with `.Execute()` for DML, or `.Query<T>()` for result sets.

### Result Mappings

- Dapper can map **strongly‑typed** results to POCOs (e.g., `QueryAsync<Customer>()`) as long as column names match your members (by convention).
- You can also map to tuples, primitives, or `dynamic` when a fixed shape isn’t practical.

## Code Example

```csharp
// Production‑ready example – querying a table that has dynamic columns
static async Task<List<Customer>> GetCustomersAsync(IDbConnection db)
{
    const string sql = @"
        SELECT CustomerId, FirstName, LastName, Email, Phone
        FROM Customers
        WHERE City = @City";

    // Dapper: parameterized query (no string concatenation)
    var customers = await db.QueryAsync<Customer>(sql, new { City = "Seattle" });
    return customers.ToList();
}
```

### Explanation

- The query string is built explicitly so the developer can see exactly what SQL runs on the database.
- Dapper binds parameters safely from an anonymous object (e.g., `new { City = "Seattle" }`), avoiding SQL injection without manual command plumbing.
- `QueryAsync<T>()` returns an `IEnumerable<T>`; materialize it with `ToList()` when you want a concrete list.

## Common "Gotchas"

1. **SQL Injection Risk** – Always parameterise queries when using raw SQL; Dapper will not protect you from un‑parameterised strings.
2. **Runtime mapping errors** – Dapper maps to your POCOs by convention; column/member mismatches show up at runtime (nulls/defaults or exceptions depending on the scenario).
3. **Misunderstanding connection pooling** – Prefer opening connections late and closing early; ADO.NET connection pooling makes this cheap. Avoid keeping a single `IDbConnection` open and shared across threads.
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
4. Create, open, and dispose `IDbConnection` instances per operation while relying on connection pooling; only re-use a connection within a single unit-of-work or transaction scope.  
5. Consider EF for most CRUD operations; fall back to Dapper only when needed.  
6. Avoid over‑engineering by using Dapper sparingly; the default EF patterns are often clearer and more maintainable for simple scenarios.
