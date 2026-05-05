# 08: Query performance basics

## What problem does this solve?

Efficient query execution is critical in data-driven applications. Poorly performing queries can lead to slow response times, increased server load, and frustrated users. Understanding how to write optimized queries helps developers ensure their applications remain responsive under varying loads.

## The Big Idea

Imagine your database as a library with millions of books. A poorly written query is like searching for a book by flipping through every page of every book—time-consuming and inefficient. An optimized query, on the other hand, uses indexes (like a catalog) to quickly locate the exact section or shelf where the desired information resides.

## Mechanics Relevant to Developers

### Key Concepts in Query Performance

- **Indexes**: These are like table of contents for your data. They allow the database engine to find records faster by organizing them based on specific columns.
- **Execution Plans**: When you run a query, the database generates an execution plan—a roadmap showing how it will retrieve and process the data. Understanding these plans helps identify bottlenecks.
- **Cost vs. Benefits**: Query optimization is about balancing performance gains with maintainability and complexity. Over-engineering can lead to harder-to-maintain queries without significant benefits.

### Common Performance Factors

1. **Data Volume**: The more data you query, the longer it takes. Filtering early (e.g., using `WHERE` clauses) reduces the amount of data processed.
2. **Joins and Relationships**: Joining multiple tables can be expensive if not done efficiently. Avoid unnecessary joins or use indexed columns to speed up joining.
3. **Sorting and Grouping**: Operations like `ORDER BY`, `GROUP BY`, and aggregate functions (`SUM`, `AVG`) are resource-intensive. Minimize their usage when possible.
4. **Pagination**: Fetching large result sets only to paginate them later is wasteful. Use techniques like server-side pagination to fetch only the required data.

## Code Example

```csharp
// Example: Optimized Query for Retrieving Products with High Sales
using (var context = new ApplicationDbContext())
{
    var highSalesProducts = await context.Products
        .Where(p => p.Sales > 100) // Filter early to reduce data volume
        .OrderByDescending(p => p.Sales) // Sort only after filtering
        .Take(10) // Paginate efficiently
        .ToListAsync();
}
```

### Explanation

- **Filter Early (`WHERE`)**: By applying the `Sales > 100` condition first, we reduce the number of records processed by subsequent operations.
- **Sort After Filtering**: Sorting only applies to filtered results, reducing unnecessary computations on irrelevant data.
- **Paginate with `.Take()`**: Instead of fetching all high-sales products and then paginating in memory, we use `.Take(10)` to fetch only the top 10 records directly from the database.

## Common "Gotchas"

1. **N+1 Queries**: When querying related entities without proper eager loading (e.g., using `Include`), each record can trigger additional queries for its relationships, leading to performance degradation.
2. **Unnecessary Joins**: Joining tables when not required or joining on non-indexed columns can slow down query execution.
3. **Sorting Large Result Sets**: Sorting large datasets without filtering first forces the database to process all records before applying sorting criteria.
4. **Ignoring Indexes**: Failing to use indexes (or creating them where needed) results in full-table scans, which are extremely inefficient for large tables.

## Opinionated Advice

- **Always Profile Queries**: Use tools like SQL Server Profiler or Entity Framework Core's `Include`/`ThenInclude` methods with caution. Measure the impact of your queries before and after optimization.
- **Use Indexes Strategically**: Create indexes on frequently queried columns, but avoid over-indexing as it can slow down write operations.
- **Minimize Data Fetching**: Only retrieve the data you need. Avoid using `SELECT *` unless absolutely necessary; specify only the required columns.
- **Batch Operations**: When performing bulk updates or deletes, use batch operations instead of individual row-by-row operations to reduce overhead.

## When Defaults Are Enough

- **Simple Queries with Small Data Sets**: For small tables and straightforward queries (e.g., fetching a single record by ID), default query behavior is often sufficient without additional optimization.
- **Read-Only Environments**: In environments where data doesn't change frequently, simple queries may not require advanced optimizations unless performance issues arise.

## Boundaries of Overkill

- **Over-indexing**: Adding indexes to every column can slow down write operations and increase storage requirements. Only index columns that are frequently queried or used in joins.
- **Premature Optimization**: Avoid optimizing queries before profiling them. Focus on writing maintainable code first, then optimize only where necessary based on performance metrics.

## Summary

Query performance is a critical aspect of building efficient data-driven applications. By understanding how indexes work, analyzing execution plans, and applying best practices like filtering early and minimizing unnecessary operations, developers can ensure their queries remain fast and scalable.

### Take-home Value

1. **Filter Early**: Reduce the amount of data processed by applying conditions (`WHERE`) as early as possible.
2. **Use Indexes Wisely**: Create indexes on frequently queried columns to speed up lookups but avoid over-indexing.
3. **Minimize Joins and Sorting**: Only join tables when necessary, and sort only after filtering to reduce computational overhead.
4. **Paginate Efficiently**: Use techniques like `.Take()` or server-side pagination instead of fetching large result sets unnecessarily.
5. **Profile Queries**: Measure query performance before optimizing to ensure optimizations are targeted where they matter most.
6. **Balance Performance with Maintainability**: Avoid over-engineering queries unless profiling shows a significant benefit, as overly complex queries can be harder to maintain.
