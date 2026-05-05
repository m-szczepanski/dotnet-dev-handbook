# 15: Seeding data safely

## What problem does this solve?

When you add new records to your database, you often want to load some sample or default data so that your application can run right away or that users can see a working example without having to create it manually.  
But loading data “by hand” can be risky: the wrong column values can corrupt the target tables, and if the script is run twice you might end up with duplicate rows or missing foreign‑key constraints being violated.  

This page explains how to load sample data safely so that your application can go live immediately with a consistent dataset, without risking corruption.

## The Big Idea

Imagine you’re building an apartment complex.  
Before tenants move in, the landlord wants every unit to have its own set of furniture – chairs, tables, maybe a couch – but he doesn’t want any two units to end up with identical furniture because that would be redundant and hard to manage.  

The “seeding” process is like this: you create a *template* (the sample data) once, then every new tenant gets the same furniture plan without having to copy it manually each time.  

In the context of .NET, we use an **asynchronous** `MigrateAsync` call that runs against the database connection string, and we wrap any exceptions so that the migration script can be retried or rolled back safely.

## Mechanics Relevant to Developers

### Using MigrateAsync for Safe Database Updates

- The code calls `await _migrationBuilder.MigrateAsync();` **asynchronously**.  
- Any exceptions thrown during the migration are caught by a `try…catch`, logged, and re‑thrown so that the host can react appropriately.

### Handling Exceptions Safely

- Wrapping the migration in a catch block ensures that partial failures (e.g., one table is updated but another fails) do not leave the database in an inconsistent state.  
- Re‑throwing the exception allows the host to decide whether to continue, abort, or attempt a rollback.

## Code Example

```csharp
// Production-ready example – safely seed sample data
var connectionString = "your_connection_string";
await using var connection = new SqlConnection(connectionString);
await using var transaction = connection.BeginTransactionAsync();

try
{
    await _migrationBuilder.MigrateAsync(transaction);   // async migration
}
catch (Exception ex)
{
    // Log the exception first, then re-throw to propagate it
    _logger.LogError(ex, "Error during database migration");
    throw;   // re-throw so host can react
}
```

### Explanation

- **`MigrateAsync`**: Asynchronously executes the SQL scripts that create or update tables.  
- **`transaction`**: Keeps the changes atomic – if any part of the script fails, the entire transaction is rolled back and the database remains consistent.  
- **`try…catch`**: Catches any exception during the migration, logs it with a descriptive message, then re‑throws the original exception so that the host application receives a clear error signal.

## Common "Gotchas"

1. **Running the script twice** – If you run `MigrateAsync` more than once without clearing the history or by manually deleting the migration files, you’ll end up with duplicate records in some tables and missing constraints in others.  
2. **Ignoring transaction scope** – If the migration code runs outside a transaction, an exception can leave the database half‑updated and vulnerable to corruption.  
3. **Not catching the right exceptions** – Catching only generic `Exception` types masks domain‑specific errors that could be handled differently.  
4. **Forgetting to log** – Without proper logging you won’t know which part of the script failed when the migration rolls back.

## Opinionated Advice

- **Always wrap migrations in a transaction** – It keeps your database consistent and makes rollbacks predictable.  
- **Use descriptive logging** – Log not just “Error” but the exact step that failed (e.g., “Failed to migrate CreateTableUsers”).  
- **Keep migration files DRY** – Reuse common script snippets instead of duplicating logic in multiple migration classes.  

## When Defaults Are Enough

- **Most short‑lived seeding scripts**: If your sample data is very small and only needs a few inserts, the default `MigrateAsync` call can be sufficient.  
- **Use built‑in sample data**: For quick demos, many ORMs (e.g., Entity Framework Core) provide sample data that you can insert with minimal custom code.

## Boundaries of Overkill

- **Overusing complex transactions** – Adding nested or overly complex transactions for trivial seeding tasks adds unnecessary cognitive load and makes the code harder to understand.  
- **Premature manual cleanup** – If your tables are not heavily constrained, you don’t need to manually drop and recreate them; just use the framework’s migration history.

## Summary

Seeding data safely means loading sample records into a database in a way that keeps it consistent, atomic, and recoverable. By using `MigrateAsync` within an asynchronous transaction and handling exceptions explicitly, developers can add default or demo data without risking corruption or duplicate records.  

### Take-home Value

1. Use `MigrateAsync` to load sample data safely.  
2. Wrap migrations in an async transaction for atomic updates.  
3. Catch and log exceptions before re‑throwing them.  
4. Avoid running the seeding script more than once unless you intentionally clear migration history.  
5. Prefer built‑in or minimal custom scripts for quick demos.  
6. Don’t over‑engineer small, short‑lived seeding tasks with nested transactions or manual cleanup.
