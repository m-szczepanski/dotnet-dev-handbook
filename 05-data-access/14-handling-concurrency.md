# 14: Handling Concurrency

## What problem does this solve?

When two or more parts of an application modify the same data concurrently, race conditions can corrupt state or lead to lost updates. This chapter explains how .NET’s built-in concurrency mechanisms protect shared data and keep your code reliable.

## The Big Idea

Imagine a restaurant kitchen where several chefs are preparing dishes that share ingredients. A lock is like a chef’s workbench—the first chef finishes, takes the bench, and only then does the next chef start using it. Without locks, one chef might remove an ingredient while another is still using it, ruining the dish.

## Mechanics Relevant to Developers

### How .NET implements locks

- **`System.Threading.Lock`**: A low‑level monitor that can be taken by multiple threads.
- **`lock` statement**: Guarantees only one thread enters at a time and automatically releases when finished.

### Using `Lock`

```csharp
private static readonly object _locker = new();
public void UpdateProductPrice(Product product, decimal price)
{
    // Enter critical section – only one thread can reach here
    lock (_locker)
    {
        if (product == null) return;

        // Business logic that updates the product's price
        ...
    }
}
```

*The `lock` statement ensures the method runs serially. When the block exits, even via an exception, the lock is released.*

### Using `Monitor.TryEnter`

```csharp
private static readonly object _locker = new();
public bool TryUpdateProductPrice(Product product, decimal price)
{
    if (!Monitor.TryEnter(_locker))
        return false;   // Lock couldn't be obtained

    try
    {
        // Business logic that updates the product's price
        ...
    }
    finally
    {
        Monitor.Exit(_locker);   // Always release the lock
    }

    return true;
}

> *`TryEnter` lets you decide whether to continue and release the lock yourself, which can be useful in asynchronous code.*

## Code Example

```csharp
using System.Threading;

class ProductService
{
    private static readonly object _locker = new();

    // This method is thread‑safe because of the monitor guard.
    public void UpdateProductPrice(Product product, decimal price)
    {
        if (product == null) return;

        lock (_locker)
        {
            // Business logic that updates the product's price
            ...
        }
    }

    // If you're writing an async method, you must be careful about exceptions.
    public Task<bool> TryUpdateProductPriceAsync(Product product,
                                                decimal price,
                                                CancellationToken token)
    {
        if (product == null) return Task.FromResult(false);

        lock (_locker)
        {
            try
            {
                // Business logic that updates the product's price
                ...
                return Task.FromResult(true);
            }
            catch (Exception e)
            {
                // You must release the monitor guard manually.
                Monitor.Exit(_locker);
                throw;   // Re‑throw original exception so caller can handle it
            }
        }
    }

    public bool TryUpdateProductPrice(Product product, decimal price)
    {
        if (!Monitor.TryEnter(_locker))
            return false;

        try
        {
            // Business logic that updates the product's price
            ...
            return true;
        }
        finally
        {
            Monitor.Exit(_locker);
        }
    }

}
```

### Explanation

- **`lock` vs `TryEnter`**: `lock` guarantees a critical section but can block other threads. `Monitor.TryEnter` lets you check if the lock is free and decide whether to proceed or return immediately.
- **Exception handling**: In async methods, you must manually call `Monitor.Exit(_locker)` in a `finally` block; otherwise, an exception would prevent the lock from being released, causing a deadlock or race condition.
- **Real‑world context**: These patterns are used when updating shared data (e.g., a product’s price) across multiple services or threads.

## Common "Gotchas"

1. **Ignoring exceptions in `lock` blocks** – If an exception occurs, the lock is released automatically, but you may lose state if you don’t handle it.
2. **Overusing locks on static collections** – Locking a collection while iterating can lead to race conditions and deadlocks.
3. **Neglecting thread‑safe alternatives (e.g., `ConcurrentBag`)** – Using non‑concurrent collections in concurrent scenarios can corrupt data.
4. **Not releasing a monitor guard manually** – In async methods, failing to release the lock can cause deadlocks.

## Opinionated Advice

- **Use `lock` for simple, short‑lived critical sections** – Prefer it over `Monitor.TryEnter` unless you have specific reasons (e.g., asynchronous code).
- **Avoid static collections without locks in concurrent scenarios** – If you must use a collection that isn’t thread‑safe, wrap all operations with a lock.
- **Profile memory usage regularly** – Locks can impact performance; monitor CPU and GC usage when adding concurrency logic.

## When Defaults Are Enough

- **Most short‑lived objects**: The .NET runtime handles short‑lived objects efficiently. In many applications, the default behavior is sufficient for data access layers that don’t share mutable state across multiple threads or services.
- **Simple CRUD operations on read‑only collections**: If your data layer only reads immutable records, you can rely on thread‑safe queries without additional locking.

## Boundaries of Overkill

- **Overusing finalizers**: Adding finalizers unnecessarily can introduce complexity and prevent the runtime from reclaiming memory efficiently. Use `Dispose` for unmanaged resources.
- **Premature optimization**: Don’t obsess over object lifetimes or manual cleanup if your application’s data access patterns are simple and read‑only.

## Summary

Handling concurrency in .NET requires understanding how locks protect shared data and how to use them safely. The chapter explains the mechanics of `lock` statements, `Monitor.TryEnter`, and common pitfalls, providing developers with practical tools for building thread‑safe code.

### Take-home Value

1. Understand whether an operation is safe for concurrent execution.  
2. Use `lock` statements for short, critical sections; prefer `TryEnter` in asynchronous methods.  
3. Be cautious about static collections without manual lock release in concurrent scenarios.  
4. Implement thread‑safe alternatives such as `ConcurrentBag` when needed.  
5. Handle exceptions carefully in `lock` blocks to avoid race conditions or deadlocks.  
6. Avoid over‑relying on manual cleanup; trust the runtime for most short‑lived objects.
