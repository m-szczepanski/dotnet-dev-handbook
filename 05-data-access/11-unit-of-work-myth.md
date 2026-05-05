# 11: The Unit of Work Myth

## What problem does this solve?

You’re building a web app, and you keep writing the same database transaction logic over and over: open connection, begin transaction, run multiple operations, commit or rollback. You start thinking, “There must be a better way.” Enter the Unit of Work pattern—promised as a clean way to group database operations. But in practice, it often adds complexity without real benefit, especially in modern .NET apps. This chapter cuts through the noise: why the classic Unit of Work is a myth in today’s ecosystem, and what you should actually do instead.

## The Big Idea

Think of the Unit of Work like a kitchen order ticket. You write down all the dishes a customer wants, and the kitchen cooks them all at once. But here’s the catch: if the kitchen is already busy, you’re just creating more tickets, not making things faster. In .NET, the database context (like `DbContext`) already acts as your order ticket. It tracks changes and knows when to commit. Adding a separate Unit of Work layer is like having a second waiter just to hand the ticket to the kitchen—it’s not wrong, but it’s unnecessary unless you’re doing something special.

## Mechanics Relevant to Developers

### The Built-in Unit of Work in `DbContext`

- **`DbContext` is already a Unit of Work**: It tracks changes across multiple entities, manages transactions, and commits them all at once via `SaveChanges()`.
- **Transactions are automatic**: If you call `SaveChanges()` once, all pending changes are committed in a single transaction—no extra setup needed.
- **No need for manual transaction management**: The framework handles it unless you’re doing something advanced like distributed transactions.

### Why a separate UOW class adds overhead

- **Extra layer of indirection**: You’re adding a class that just forwards calls to `DbContext`.
- **Harder to debug**: When things go wrong, you have two layers to trace instead of one.
- **Breaks tooling and DI integration**: Tools like Entity Framework Core’s change tracking and migrations expect `DbContext` to be the source of truth.

## Code Example

```csharp
// Realistic scenario: updating a user and their profile in one operation
public class UserService
{
    private readonly AppDbContext _context;

    public UserService(AppDbContext context)
    {
        _context = context;
    }

    public async Task<bool> UpdateUserAndProfileAsync(
        int userId,
        string name,
        string bio,
        string avatarUrl)
    {
        // Fetch the user and profile
        var user = await _context.Users
            .FirstOrDefaultAsync(u => u.Id == userId);

        if (user == null) return false;

        // Update properties
        user.Name = name;
        user.LastUpdated = DateTime.UtcNow;

        // Update profile
        var profile = await _context.UserProfiles
            .FirstOrDefaultAsync(p => p.UserId == userId);

        if (profile != null)
        {
            profile.Bio = bio;
            profile.AvatarUrl = avatarUrl;
            profile.LastUpdated = DateTime.UtcNow;
        }

        // Save all changes in one call
        var result = await _context.SaveChangesAsync();

        return result > 0;
    }
}
```

### Explanation

- **No separate Unit of Work**: This code directly uses `DbContext`, which already handles batching and transactionality.
- **Single `SaveChangesAsync()` call**: Ensures all changes are committed atomically.
- **No extra interfaces or abstractions**: Avoids over-engineering for a simple task.
- **Real-world data flow**: Fetches related data, updates it, and saves once—exactly how most apps work.

## Common "Gotchas"

1. **Creating a Unit of Work class just because it’s “best practice”**: You’re adding complexity for no real benefit.
2. **Trying to inject `IUnitOfWork` when you only need `DbContext`**: This breaks tooling and makes testing harder.
3. **Overcomplicating testing**: You now have to mock both `IUnitOfWork` and `DbContext`, instead of just one.
4. **Misunderstanding transaction scope**: Assuming you need explicit transactions when `SaveChanges()` already manages them.

## Opinionated Advice

- **Use `DbContext` directly as your Unit of Work**: It already does the job better than most custom implementations.
- **Avoid abstracting `DbContext` unless you’re doing multi-tenancy or complex data routing**: Even then, consider `DbContext` with a factory or strategy pattern.
- **Don’t add `IUnitOfWork` interfaces unless you’re building a framework or library**: For most apps, it’s just noise.
- **If you need transaction control, use `TransactionScope` or `BeginTransaction()` explicitly**: Don’t rely on a fake UOW layer.

## When Defaults Are Enough

- **Simple CRUD operations**: Updating a user, creating an order, deleting a post—just use `SaveChanges()` directly.
- **Single database per app**: If you’re not dealing with multiple databases or distributed systems, the default behavior is sufficient.
- **You’re not building a reusable library or framework**: If this is an internal app, don’t overthink it.

## Boundaries of Overkill

- **Adding `IUnitOfWork` and `IRepository` interfaces for a single service**: This is a classic anti-pattern—creates 10x more code for no gain.
- **Using a UOW pattern in a single-context app**: You’re not saving time—you’re adding confusion.
- **Trying to “follow the pattern” without understanding the real problem**: If you’re not solving a real issue, don’t fix something that isn’t broken.

## Summary

The Unit of Work pattern is a myth in modern .NET apps. `DbContext` already manages change tracking, transactions, and batching—doing everything a traditional UOW would. Adding a separate layer only adds complexity, makes debugging harder, and breaks tooling. Use `SaveChanges()` directly, and only consider abstractions if you’re building a large-scale system with complex data routing or multi-tenancy.

### Take-home Value

1. `DbContext` is your real Unit of Work—use it directly.
2. Don’t add `IUnitOfWork` unless you’re building a framework.
3. `SaveChanges()` handles transactions automatically.
4. Avoid `IRepository` and `IUnitOfWork` for simple apps.
5. Extra abstraction = more bugs, more testing, more confusion.
6. When in doubt, just use `DbContext`—it’s been battle-tested.
