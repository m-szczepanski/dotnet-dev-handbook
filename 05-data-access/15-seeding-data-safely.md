# 15: Seeding data safely

## What problem does this solve?

When you add new records to your database, you often want to load some sample or default data so that your application can run right away or that users can see a working example without having to create it manually.  
But loading data “by hand” can be risky: the wrong column values can corrupt the target tables, and if the script is run twice you might end up with duplicate rows or missing foreign‑key constraints being violated.  

This page explains how to load sample data safely so that your application can go live immediately with a consistent dataset, without risking corruption.

## The Big Idea

Imagine you’re building an apartment complex.  
Before tenants move in, the landlord wants every unit to have its own set of furniture – chairs, tables, maybe a couch – but he doesn’t want any two units to end up with identical furniture because that would be redundant and hard to manage.  

The “seeding” process is like this: you create a *template* (the sample data) once, then every new tenant gets the same furniture plan without having to copy it manually each time.  

In the context of .NET, a common approach is to **apply EF Core migrations** with `Database.MigrateAsync()` and then run **idempotent seeding** (so reruns don’t create duplicates).

## Mechanics Relevant to Developers

### Applying migrations and seeding safely

- The code calls `await context.Database.MigrateAsync();` to apply pending migrations (this is safe to run repeatedly; applied migrations are tracked).  
- Seeding logic should be **idempotent** (e.g., check if data exists before inserting) to avoid duplicates.

### Handling Exceptions Safely

- Wrapping the *seeding* in a catch block ensures that partial failures (e.g., one insert succeeds but a later insert fails) do not leave the database in an inconsistent state.  
- Re‑throwing the exception allows the host to decide whether to continue, abort, or attempt a rollback.

## Code Example

```csharp
// Production-ready example – safely seed sample data
var connectionString = "your_connection_string";
await using var context = new ApplicationDbContext(
    new DbContextOptionsBuilder<ApplicationDbContext>()
        .UseSqlServer(connectionString)
        .Options);

// Safe to re-run: applied migrations are tracked in __EFMigrationsHistory
await context.Database.MigrateAsync();

await using var transaction = await context.Database.BeginTransactionAsync();

try
{
    // Idempotent seeding: only insert if missing
    if (!await context.Products.AnyAsync())
    {
        // Seed data here
        ...
        await context.SaveChangesAsync();
    }

    await transaction.CommitAsync();
}
catch (Exception ex)
{
    await transaction.RollbackAsync();
    // Log the exception first, then re-throw to propagate it
    _logger.LogError(ex, "Error during database seeding");
    throw;   // re-throw so host can react
}
```

### Explanation

- **`MigrateAsync`**: Applies any pending EF Core migrations; it’s designed to be safely re-run.
- **`transaction`**: Keeps the *seeding* changes atomic – if any part fails, the transaction is rolled back and the database remains consistent.
- **`try…catch`**: Catches any exception during seeding, logs it with a descriptive message, then re‑throws the original exception so that the host application receives a clear error signal.

## Common "Gotchas"

1. **Non-idempotent seeding** – `Database.MigrateAsync()` is safe to run multiple times; duplicate rows usually come from seeding code that inserts unconditionally.
2. **Ignoring transaction scope** – If the seeding code runs outside a transaction, an exception can leave the database half‑updated and vulnerable to corruption.  
3. **Not catching the right exceptions** – Catching only generic `Exception` types masks domain‑specific errors that could be handled differently.  
4. **Forgetting to log** – Without proper logging you won’t know which part of the script failed when the migration rolls back.

## Opinionated Advice

- **Make seeding idempotent** – Prefer existence checks (or upserts) so running the seed multiple times is safe.  
- **Wrap complex seeding in a transaction** – It keeps the seed atomic and makes failures easier to roll back.
- **Use descriptive logging** – Log not just “Error” but the exact step that failed (e.g., “Failed to migrate CreateTableUsers”).  
- **Keep migration files DRY** – Reuse common script snippets instead of duplicating logic in multiple migration classes.  

## When Defaults Are Enough

- **Most short‑lived seeding scripts**: If your sample data is very small and only needs a few inserts, `Database.MigrateAsync()` plus a simple idempotent seed is usually sufficient.  
- **Use built‑in sample data**: For quick demos, many ORMs (e.g., Entity Framework Core) provide sample data that you can insert with minimal custom code.

## Boundaries of Overkill

- **Overusing complex transactions** – Adding nested or overly complex transactions for trivial seeding tasks adds unnecessary cognitive load and makes the code harder to understand.  
- **Premature manual cleanup** – If your tables are not heavily constrained, you don’t need to manually drop and recreate them; just use the framework’s migration history.

## Summary

Seeding data safely means applying migrations and loading sample records in a way that keeps the database consistent, atomic, and recoverable. By using `Database.MigrateAsync()` and idempotent seeding within a transaction (when appropriate), developers can add default or demo data without risking corruption or duplicate records.  

### Take-home Value

1. Use `Database.MigrateAsync()` to apply migrations safely.  
2. Wrap multi-step seeding in an async transaction for atomic updates.  
3. Catch and log exceptions before re‑throwing them.  
4. Make seeding idempotent so reruns don’t create duplicates.  
5. Prefer built‑in or minimal custom scripts for quick demos.  
6. Don’t over‑engineer small, short‑lived seeding tasks with nested transactions or manual cleanup.
