# 17: Data access best practices

## What problem does this solve?

Developers often struggle with writing efficient, maintainable, and secure data‑access code.  
Common pain points include:

* **Security risks** – accidental SQL injection or other query flaws.  
* **Performance bottlenecks** – poorly written queries that return large result sets or cache misses.  
* **Maintenance headaches** – tightly coupled infrastructure code that is hard to test or refactor.

By following established best practices, you can avoid these issues and ship cleaner, faster, safer data‑access layers.

## The Big Idea

Imagine the database as a library with thousands of books (records).  

* **SQL queries** are like quick‑search tools – they should return only what you need.  
* **ORMs / EF Core** act like smart librarians that keep track of which books have been borrowed (entities) and when to return them (caching).  
* A **centralized repository pattern** keeps the library’s rules in one place, so everyone follows the same catalog (common data‑access patterns).

## Mechanics Relevant to Developers

### Centralize your repositories

 **Why**: Keeps all data‑access logic in one location, making tests easier and reducing duplicated code.  
 **How**: Create a single namespace or assembly that exposes repository interfaces for each domain entity.

### Keep infrastructure out of business logic

 **Why**: Prevents “spaghetti” code where business rules are mixed with data‑access concerns.  
 **How**: Use dependency injection to inject repositories into controllers, services, or other layers.

### Optimize query patterns

 **Avoid N+1 queries** – always load related entities in a single query (`Include`, `ThenInclude`, or explicit eager loading).  
 **Use projection** when you only need certain columns (e.g., `.Select(e => new { e.Id, e.Name })`).  
 **Leverage caching** to reduce round‑trips for frequently accessed data.

### Secure queries

 **Use parameterized commands** – never concatenate user input into raw SQL strings.  
 **Validate inputs** before sending them to the database layer.

## Code Example

```csharp
// Production-ready example using EF Core 8+ (Modern .NET)
public interface IProductRepository
{
    Task<Product> FindAsync(int id);
    Task<IEnumerable<Product>> GetAllAsync();
}

public class ProductRepository : IProductRepository
{
    private readonly ApplicationDbContext _context;

    public ProductRepository(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<Product> FindAsync(int id)
    {
        return await _context.Products
            .Include(p => p.PurchaseOrder)
            .SingleOrDefaultAsync(p => p.Id == id);
    }

    public async Task<IEnumerable<Product>> GetAllAsync()
    {
        // Project only needed columns for performance
        return await _context.Products
            .Select(p => new Product { Id = p.Id, Name = p.Name })
            .ToListAsync();
    }
}
```

### Explanation

 **Interface definition**: Keeps the contract decoupled from EF Core internals.  
 **EF Core query pattern**: Uses `.Include` for eager loading of related entities (`PurchaseOrder`).  
 **Projection optimization**: Returns only `Id` and `Name` instead of the full entity, reducing payload size.  
 **Lifetime**: In typical ASP.NET Core apps, `ApplicationDbContext` is registered as *scoped* and is disposed by the DI container. The repository should not dispose an injected context.

## Common "Gotchas"

1. **Ignoring N+1 queries** – forgetting to include related entities can cause many round‑trips and performance drops.  
2. **Mixing infrastructure into business logic** – putting EF Core calls inside services or controllers can make tests brittle.  
3. **Not validating inputs before database calls** – allowing user input directly into SQL strings opens SQL injection vectors.  
4. **Over‑using finalizers for disposal** – disposing in a `finally` block instead of implementing `IDisposable` can lead to leaks.

## Opinionated Advice

 **If you create a `DbContext` yourself, wrap it in `using`. If it’s injected, let DI manage its disposal.**  
 **Use repository interfaces to keep data‑access logic isolated from the rest of the application.**  
 **Profile query execution plans** to catch unexpected performance hits early.  
 **Keep `DbContext` short‑lived** (scoped per request/operation). For background work, prefer `IDbContextFactory<TContext>`.

## When Defaults Are Enough

 **Most simple CRUD operations**: Using LINQ queries with minimal projections is usually sufficient for lightweight data‑access scenarios.  
 **Read‑only APIs**: If you only need to return lists of entities, the default EF Core query patterns work fine without extra optimization layers.

## Boundaries of Overkill

 **Over‑engineering caching** – adding manual caches or custom cache abstractions before you’ve measured a real bottleneck.  
 **Premature finalizer use**: Adding `~` operators for disposal that isn’t truly necessary can introduce unnecessary complexity and bugs.

## Summary

Following data‑access best practices ensures your code is secure, performant, and maintainable. By centralizing repositories, keeping infrastructure separate from business logic, optimizing query patterns, and securing queries, you can avoid common pitfalls such as N+1 queries, security flaws, and hard‑to‑maintain layers.

### Take-home Value

1. Centralize your repositories to keep data‑access code in one place.  
2. Keep infrastructure out of business logic for easier testing and maintenance.  
3. Optimize query patterns by avoiding N+1 queries and using projection.  
4. Secure queries with parameterized commands and input validation.  
5. Profile query execution plans to catch performance issues early.  
6. Avoid over‑engineering caching or premature finalizer use – start simple and scale only when needed.
