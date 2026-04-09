# 06: Tracking vs No-Tracking Queries

## What problem does this solve?

When working with Entity Framework (EF), one of the most common decisions developers face is whether to use **tracking** or **no-tracking** for their database queries. This choice impacts memory usage, performance, and how EF manages changes to entities in your application. Understanding when to use each mode ensures that you optimize both resource utilization and developer productivity.

## The Big Idea

Think of Entity Framework as a librarian managing books (entities) in a library (database). When you ask the librarian for a book:

- **Tracking**: The librarian keeps an eye on every book you borrow, ensuring it's returned properly. This is helpful if you plan to make changes and save them back.
- **No-Tracking**: The librarian simply hands over the book without keeping track of it. You can read it freely, but if you want changes saved later, you must explicitly "check the book back in" (attach it) first.

This analogy highlights that tracking provides more features (like change detection) at the cost of memory, while no-tracking is leaner and faster for scenarios where you only need to read data.

## Mechanics Relevant to Developers

### Entity Framework's Context Behavior

Entity Framework maintains a **change tracker** in its `DbContext` instance. This tracker monitors entities that are loaded into memory so it can detect changes when you call methods like `SaveChanges()`.

- **Tracking**: Entities are added to the change tracker, allowing EF to track any modifications made to them.
  - **Pros**: Automatic detection of changes for updates or deletions.
  - **Cons**: Higher memory usage and slower queries due to additional overhead.
- **No-Tracking**: Entities are not added to the change tracker. They're treated as read-only snapshots from the database.
  - **Pros**: Lower memory footprint and faster query execution since EF doesn't track changes.
  - **Cons**: Changes to these entities are not detected automatically. To persist updates, you must attach the entity and mark it as modified (or use `Update`).

### How to Use Tracking vs No-Tracking

You can control whether a query uses tracking or no-tracking by calling the `.AsNoTracking()` method on your query:

```csharp
// Tracking (default behavior)
var trackedCustomers = context.Customers.ToList();

// No-Tracking
var nonTrackedCustomers = context.Customers.AsNoTracking().ToList();
```

### When to Use Each Mode

- **Use Tracking** when:
  - You need to modify entities and save changes back to the database.
  - You're working with a small number of entities, as tracking doesn't cause significant memory overhead.
- **Use No-Tracking** when:
  - You only need to read data (e.g., displaying in a report or UI).
  - Performance is critical, especially for large datasets where tracking would slow down queries and consume more memory.

## Code Example

Here's an example demonstrating the difference between tracking and no-tracking:

```csharp
using Microsoft.EntityFrameworkCore;

public class CustomerService
{
    private readonly MyDbContext _context;

    public CustomerService(MyDbContext context)
    {
        _context = context;
    }

    // Tracking query (default behavior)
    public List<Customer> GetCustomersForEditing()
    {
        return _context.Customers.ToList(); // Entities are tracked by default
    }

    // No-Tracking query
    public List<Customer> GetCustomersForDisplaying()
    {
        return _context.Customers.AsNoTracking().ToList(); // Entities are not tracked
    }
}
```

### Explanation

- **GetCustomersForEditing**: This method returns a list of `Customer` entities that are tracked by EF. If you modify these entities and call `_context.SaveChanges()`, EF will detect the changes and update the database accordingly.
- **GetCustomersForDisplaying**: This method uses `.AsNoTracking()` to fetch customers without tracking them. Direct modifications on these detached entities are not auto-detected by `SaveChanges()` unless you attach them first. This approach is faster and consumes less memory for read paths.

## Common "Gotchas"

1. **Accidental Modifications on No-Tracking Entities**:
  - If you modify a no-tracking entity and call `_context.SaveChanges()` without attaching it, EF usually persists nothing because the context isn't tracking that entity.
2. **Performance Issues with Large Datasets in Tracking Mode**:
   - When querying large datasets, tracking can lead to significant memory usage and slower query execution times.
3. **Forgetting to Use AsNoTracking for Read-Only Operations**:
   - If you're only reading data (e.g., displaying a list of products), using tracking unnecessarily consumes resources without providing any benefit.

## Opinionated Advice

- **Always Default to No-Tracking**: Start with `.AsNoTracking()` unless you explicitly need change detection or plan to modify entities.
- **Use Tracking Only When Necessary**: Reserve tracking for scenarios where you intend to update or delete entities. This minimizes memory overhead and improves performance.
- **Profile Memory Usage**: If your application is handling large datasets, profile the memory usage of your queries to ensure that tracking isn't causing unnecessary resource consumption.

## When Defaults Are Enough

- **Read-Only Queries**: For simple read operations (e.g., displaying data in a grid or report), no-tracking is sufficient and optimal.
- **Small Datasets**: If you're working with small datasets, the overhead of tracking might be negligible. In such cases, sticking to default behavior (tracking) won't hurt performance.

## Boundaries of Overkill

- **Overusing No-Tracking for Entities You Plan to Modify**:
  - If you fetch entities using `.AsNoTracking()` and later modify them, EF will not detect those changes unless you attach/mark them as modified. Use tracking when you need automatic change detection.
- **Ignoring Memory Constraints with Tracking**:
  - For large datasets or applications with memory constraints, relying on tracking can lead to performance issues. Be mindful of the trade-offs.

## Summary

Tracking vs no-tracking is a fundamental decision in Entity Framework that affects both memory usage and functionality. By default, EF tracks entities to enable change detection, but this comes at a cost. No-tracking provides a leaner approach for read-only operations or scenarios where performance is critical. Understanding when to use each mode ensures you strike the right balance between flexibility and efficiency.

### Take-home Value

1. **Use `.AsNoTracking()` by default** unless you need change detection.
2. **Understand the trade-offs**: Tracking provides more features but consumes more memory, especially with large datasets.
3. **Know when to track**: Use tracking only for entities that you plan to modify and save back to the database.
4. **Profile your queries**: Measure performance and memory usage to ensure you're making optimal decisions based on your application's needs.
5. **Handle detached updates explicitly**: If you update no-tracking entities, attach them (or use `Update`) so `SaveChanges()` can persist the changes.
6. **Optimize for read-heavy scenarios**: For operations that only involve reading data, always use `.AsNoTracking()` to improve performance and reduce memory usage.
