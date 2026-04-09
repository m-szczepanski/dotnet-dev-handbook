# 03: Entity Framework Core Overview

## What problem does this solve?

Developers often struggle with writing efficient, maintainable database access code. Managing SQL queries manually can lead to repetitive boilerplate, errors in mapping data between objects and databases, and performance issues like N+1 query problems. Entity Framework Core (EF Core) simplifies these challenges by providing an **ORM (Object-Relational Mapping)** layer that automates much of the heavy lifting involved in interacting with relational databases.

## The Big Idea

Think of EF Core as a translator between your application's objects and the database. Instead of writing raw SQL queries, you work with familiar C# classes (entities). EF Core takes care of converting these objects into SQL statements under the hood, executing them against the database, and mapping results back to your objects. It’s like having a personal assistant who understands both human language (your code) and database dialects.

## Mechanics Relevant to Developers

### Key Concepts in EF Core

- **Entities**: These are C# classes that represent tables in your database. Each property of an entity maps to a column.
- **DbContext**: This is the central class that manages the connection to the database, tracks changes to entities, and executes queries.
- **Querying with LINQ**: Instead of writing SQL, you use LINQ (Language Integrated Query) to express queries in C#. EF Core translates these into efficient SQL statements.

### How It Works

1. **Mapping Entities to Tables**:
   - You define your entity classes using plain C#.
   - Using attributes or fluent API configurations, you map these entities to database tables and columns.
2. **Generating Queries**:
   - When you query data (e.g., `context.Users.Where(u => u.IsActive)`), EF Core translates this into SQL under the hood.
   - It optimizes queries by generating efficient SQL statements based on your LINQ expressions.
3. **Change Tracking**:
   - EF Core tracks changes to entities in memory and automatically generates UPDATE, INSERT, or DELETE statements when you call `SaveChanges()`.

### Database Providers

EF Core supports multiple database providers (e.g., SQL Server, PostgreSQL, SQLite). You can switch between them without changing your application code by simply updating the connection string and provider configuration.

## Code Example

```csharp
using Microsoft.EntityFrameworkCore;

// Define an entity class
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;
    public decimal Price { get; set; }
}

// Define a DbContext
public class StoreContext : DbContext
{
    public DbSet<Product> Products { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        // Configure the database provider and connection string
        optionsBuilder.UseSqlServer("Server=(localdb)\\mssqllocaldb;Database=StoreDb;Trusted_Connection=True;");
    }
}

// Usage example: Querying data
using (var context = new StoreContext())
{
    var products = context.Products.Where(p => p.Price > 50).ToList();
    
    foreach (var product in products)
    {
        Console.WriteLine($"{product.Name} - {product.Price}");
    }
}
```

### Explanation

- **Entity Definition**: The `Product` class represents a table named `Products` in the database. Each property corresponds to a column.
- **DbContext Configuration**: The `StoreContext` manages the connection to the SQL Server database and exposes a set of entities (`DbSet<Product>`).
- **Querying with LINQ**: Instead of writing raw SQL, we use LINQ to express our query (`Where(p => p.Price > 50)`). EF Core translates this into an efficient SQL statement.
- **Change Tracking**: If you modify any `Product` objects and call `SaveChanges()`, EF Core will automatically generate the necessary UPDATE statements.

## Common "Gotchas"

1. **N+1 Query Problem**:
   - When querying related data (e.g., products with their categories), EF Core may issue multiple queries to fetch related entities. Prefer eager loading (`.Include()`), projection (`.Select(...)`), or explicit loading to avoid N+1 round trips.
2. **Entity Tracking Overhead**:
   - By default, EF Core tracks all entities in memory for change detection. For large datasets, consider disabling tracking (`AsNoTracking()`) when you don’t need to update data.
3. **Performance with Raw SQL Queries**:
   - While EF Core is powerful, it’s not always optimal for complex queries. In such cases, use `.FromSqlRaw()` or raw SQL commands directly.
4. **Database Schema Migrations**:
   - When changing entity definitions (e.g., adding a new property), you must apply migrations to update the database schema. Failing to do so can lead to runtime errors.

## Opinionated Advice

- **Use Entity Framework for CRUD Operations**: For simple create, read, update, and delete operations, EF Core is highly efficient and reduces boilerplate code.
- **Optimize Queries with `.Include()` or Projections**: Always consider the N+1 query problem when fetching related data. Prefer eager loading and projections; use lazy loading only when explicitly enabled and carefully controlled.
- **Profile Query Performance**: Regularly inspect generated SQL queries using tools like EF Core’s logging or database profiling tools to ensure optimal performance.
- **Avoid Overusing Raw SQL**: While raw SQL is available, use it sparingly and only when necessary. Let EF Core handle most of your query logic.

## When Defaults Are Enough

- **Simple CRUD Operations**: For basic create, read, update, and delete operations on simple entities, the default behavior of EF Core is usually sufficient.
- **Basic Relationships**: If you’re working with one-to-many or many-to-one relationships without complex requirements, the default configurations often work well.

## Boundaries of Overkill

- **Overcomplicating Simple Queries**:
  - For straightforward queries that don’t require advanced optimizations, using EF Core’s built-in features is sufficient. Avoid over-engineering by writing raw SQL unless necessary.
- **Ignoring Performance for Complex Queries**:
  - While EF Core excels at simple queries, it may not be optimal for highly complex operations. In such cases, consider switching to raw SQL or specialized query tools.

## Summary

Entity Framework Core is a powerful ORM that simplifies database access by abstracting away much of the complexity involved in writing and managing SQL queries. By working with familiar C# classes (entities) instead of raw SQL, developers can focus on building business logic while EF Core handles data mapping and query optimization.

### Take-home Value

1. **Entities Represent Tables**: Define your entities as plain C# classes that map to database tables.
2. **DbContext Manages Connections**: Use `DbContext` to manage the connection to your database and track changes to entities.
3. **Query with LINQ, Not SQL**: Express queries using LINQ instead of writing raw SQL; EF Core translates these into efficient SQL statements.
4. **Watch Out for N+1 Queries**: Be mindful of fetching related data efficiently using `.Include()`, projections, or explicit loading. Lazy loading is opt-in and can cause N+1 queries if misused.
5. **Profile Generated SQL**: Regularly inspect the SQL generated by EF Core to ensure optimal performance and avoid unnecessary queries.
6. **Use Raw SQL Sparingly**: Leverage raw SQL only when necessary, as EF Core handles most query logic effectively.
