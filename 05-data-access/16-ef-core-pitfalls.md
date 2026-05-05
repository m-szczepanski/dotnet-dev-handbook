# 16: Common EF Core pitfalls

## What problem does this solve?

Understanding the most common mistakes developers make when using Entity Framework Core is essential for building reliable data‑access layers. These pitfalls lead to runtime failures, performance regressions, and subtle bugs that surface only after deployment or under heavy load.

## The Big Idea

Think of your EF Core queries as a recipe. A recipe can look perfect on paper but, if you skip an ingredient or misread the instructions, the dish turns out wrong. Similarly, many EF Core scenarios appear correct at first glance—until you run the query in production and discover a missing join, an incorrect parameter type, or an unsupported SQL fragment.

## Mechanics Relevant to Developers

### How queries are translated into SQL

- **Translation happens before execution**: EF Core translates LINQ expressions into SQL behind the scenes.
- **The generated SQL can be inspected** via `LoggerFactory` or the `dotnet ef database logger`.
- **Query‑plan caching** means repeated identical queries run against the same context may reuse cached plans, affecting performance.

### The importance of query‑plan caching

- Caching reduces round‑trips to the database and can improve response times for common queries.
- However, if a query changes in subtle ways (e.g., an added property), the plan is invalidated, negating the benefit.

## Code Example

```csharp
// Production-ready example that demonstrates a common pitfall
public async Task<Blog> GetLatestBlogAsync()
{
    // Pitfall: eager loading can create very large result sets (and may lead to unexpected query shapes)
    return await _context.Blogs
        .Include(blog => blog.Posts)
            .ThenInclude(p => p.Author)
        .OrderByDescending(b => b.CreatedAt)
        .FirstOrDefaultAsync();
}
```

### Explanation

- **Short‑circuiting with FirstOrDefaultAsync**: The example intentionally uses `FirstOrDefaultAsync` to illustrate that the query will only return one row, which can be a pitfall if the developer expects multiple results.
- **Eager loading tradeoffs**: `Include`/`ThenInclude` is *not* an N+1 pattern by itself. It usually results in one query (or a small fixed number of queries when using split queries), but it can still be expensive by returning a lot of duplicated data or materializing large graphs.

## Common "Gotchas"

1. **N+1 query problems** – Most commonly caused by lazy loading or issuing per‑entity queries in a loop; eager loading with `Include` is one way to prevent it.
2. **Ignoring the translation process** – Assuming a LINQ expression will always translate to SQL, not checking the logger.
3. **Over‑relying on caching** – Not considering that repeated queries with slight differences may invalidate cached plans.
4. **Misusing Async methods** – Using synchronous equivalents (`FirstOrDefault`) instead of their async counterparts.

## Opinionated Advice

- **Prevent N+1 intentionally**: use `Include` when you truly need the graph, or project (`Select`) into a read model when you only need a subset.
- **Log EF Core queries** to verify the SQL that is actually executed and compare it against your LINQ expression.
- **Profile query‑plan caching**: Monitor repeated query execution times to see if cached plans are being reused.
- **Keep server-side filtering in `IQueryable`** when you want execution in the database; use `AsEnumerable()` only for explicit client-side post-processing after you’ve already narrowed the result set.

## When Defaults Are Enough

- For simple CRUD operations, you can rely on EF Core’s default query‑translation behavior. In such cases, the built‑in caching is usually sufficient.
- If your application performs only a few queries per user session and those queries are deterministic, you may not need to tune the query plan manually.

## Boundaries of Overkill

- **Over‑engineering eager loading**: Adding excessive `Include` calls or using complex join expressions when the business logic does not require them can introduce unnecessary complexity and potential bugs.
- **Premature optimization**: Tuning EF Core queries with advanced techniques (e.g., raw SQL, query plan analysis) before you have a clear understanding of your data‑access patterns may lead to overkill.

## Summary

EF Core is powerful but introduces common pitfalls that developers must recognize. By understanding how LINQ expressions are translated into SQL and by logging or profiling the generated queries, you can avoid N+1 issues, misused async methods, and incorrect eager loading. Caching of query plans can improve performance but requires careful consideration.

### Take‑home Value

- **Identify N+1 queries** in your data‑access layers to prevent performance regressions.
- **Log EF Core queries** to verify the SQL that is actually executed against the database.
- **Understand when query‑plan caching is beneficial** and how it can be invalidated by small changes to a query.
- **Avoid over‑engineering eager loading or complex joins** unless your business logic explicitly requires them.
- **Use async methods correctly**, ensuring you don’t mix synchronous equivalents with asynchronous execution.
- **Profile repeated query executions** to see if cached plans are being reused in production scenarios.
