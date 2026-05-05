# 12: DTOs vs Entities

## What problem does this solve?

You're building a web API and keep exposing your database models directly to clients. Suddenly, you’re getting complaints about slow responses, security leaks, and endless refactoring when the database schema changes. The real pain? Your domain model is leaking into the API contract, making it fragile and hard to evolve. This chapter solves that by showing how to separate your data access concerns using DTOs (Data Transfer Objects) instead of exposing entities directly.

## The Big Idea

Think of your database as a vault with locked boxes (entities). The API is the front desk where you hand out copies of what’s inside. If you hand out the actual boxes (entities), someone could try to take the whole vault. Instead, you make a clean, simple copy (DTO) of just the parts needed—like a receipt with only the transaction details. This way, you control what gets shared, and you can change the vault or the receipt independently.

## Mechanics Relevant to Developers

### Why Entities Are Not for Public APIs

- Entities are designed for persistence and business logic.
- They often include navigation properties, shadow properties, and EF Core internals that should never leave the server.
- Exposing them publicly breaks encapsulation and creates tight coupling.

### How DTOs Work

- DTOs are plain POCOs (Plain Old C# Objects) with no logic.
- They define only the data you want to send or receive.
- You map from entities to DTOs (and vice versa) using tools like AutoMapper, or manually with `Select` or `new` expressions.

### Performance Implications

- Manual mapping (via `new`) is faster and safer than reflection-based libraries.
- AutoMapper adds overhead and complexity; use it only when you have many mappings.
- Avoid mapping large graphs—only select what’s needed.

### When to Use Each

- Use entities for internal logic, repositories, and domain operations.
- Use DTOs for API responses, request bodies, and external contracts.

## Code Example

```csharp
// DTO: Only what the client needs
public record CustomerDto(
    int Id,
    string Name,
    string Email,
    string PhoneNumber,
    DateTime CreatedAt
);

// Entity: Full domain model with persistence concerns
public class CustomerEntity
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public string Email { get; set; } = string.Empty;
    public string PhoneNumber { get; set; } = string.Empty;
    public DateTime CreatedAt { get; set; }
    public DateTime? UpdatedAt { get; set; }

    // Navigation property (not exposed in DTO)
    public List<OrderEntity> Orders { get; set; } = new();
}

// Controller: Maps from entity to DTO
[ApiController]
[Route("api/customers")]
public class CustomersController : ControllerBase
{
    private readonly AppDbContext _context;

    public CustomersController(AppDbContext context) => _context = context;

    [HttpGet("{id}")]
    public async Task<ActionResult<CustomerDto>> GetCustomer(int id)
    {
        var customer = await _context.Customers
            .Where(c => c.Id == id)
            .Select(c => new CustomerDto(
                c.Id,
                c.Name,
                c.Email,
                c.PhoneNumber,
                c.CreatedAt
            ))
            .FirstOrDefaultAsync();

        if (customer is null)
            return NotFound();

        return Ok(customer);
    }
}
```

### Explanation

- **`CustomerDto`**: A minimal, immutable record with only fields needed by the frontend.
- **`CustomerEntity`**: Full domain model with navigation properties (e.g., `Orders`) that are internal to the system.
- **LINQ Projection (`Select`)**: Maps directly from entity to DTO in the database query—no loading full entity into memory.
- **No AutoMapper**: Avoids dependency and overhead; performance is better and easier to debug.
- **Immutability**: Using `record` ensures data integrity and prevents accidental mutation.

## Common "Gotchas"

1. **Mapping All Fields Without Purpose**: Copying every field from entity to DTO, including ones not used by the client, increases payload size and attack surface.
2. **Exposing Sensitive Fields**: Accidentally including `PasswordHash`, `SSN`, or `SecretKey` in DTOs due to lack of review.
3. **Using Entities in API Responses**: Causing serialization issues (e.g., circular references) or exposing internal EF Core metadata.
4. **Over-Reliance on AutoMapper**: Introducing hard-to-debug issues when mappings break silently or perform poorly.

## Opinionated Advice

- **Never return entities directly from API endpoints**: Always project to a DTO.
- **Use `record` for DTOs**: They’re immutable, concise, and built for data transfer.
- **Map at the query level**: Use `Select` in LINQ to project only needed data—avoid loading full entities.
- **Avoid complex mapping libraries unless necessary**: Manual `new` expressions are faster, clearer, and more predictable.
- **Validate DTOs in request bodies**: Use `[FromBody]` with validation attributes (e.g., `[Required]`, `[EmailAddress]`).

## When Defaults Are Enough

- **Simple APIs with few fields**: A single `Get` endpoint with 2–3 fields? Just use `new` in `Select`.
- **Internal microservices**: If you control both ends and don’t need strict decoupling, DTOs may be overkill.
- **Prototyping or MVPs**: Skip DTOs temporarily to speed up development—but plan to add them early.

## Boundaries of Overkill

- **One-off internal tools**: If you're building a private service used only by one app, and the schema is stable, you might skip DTOs.
- **Using AutoMapper for trivial mappings**: If you're mapping 3 fields with `new`, don’t add a library.
- **Creating 100+ DTOs for every entity**: If you’re creating a DTO per entity with no reuse, you’re over-engineering. Consider a shared schema or a generic mapper.

## Summary

Separating DTOs from entities prevents data leakage, improves performance, and makes your API easier to evolve. It’s not just about security—it’s about decoupling your domain from your API contract.

### Take-home Value

1. Never expose entities directly in API responses.
2. Use `record` for DTOs—immutable, concise, and safe.
3. Map data using `Select` in LINQ to avoid loading full entities.
4. Only include fields the client actually needs.
5. Avoid AutoMapper unless you have many complex mappings.
6. Treat DTOs as contracts—change them carefully and version them when needed.
