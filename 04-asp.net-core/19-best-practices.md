# 19: ASP.NET Core Best Practices

## What problem does this solve?

ASP.NET Core is a powerful framework for building web applications, but its flexibility can sometimes lead to inconsistencies or suboptimal designs. Developers often face challenges in maintaining code quality, performance, and scalability as their projects grow. This chapter aims to provide practical guidance on best practices that help developers build robust, maintainable, and efficient ASP.NET Core applications.

## The Big Idea

Think of building an ASP.NET Core application like constructing a house. Just as you wouldn't use mismatched materials or skip foundational steps when building a house, following best practices ensures your application is well-structured, scalable, and resilient to future changes. These principles act as guidelines that help developers make informed decisions without reinventing the wheel.

## Mechanics Relevant to Developers

### 1. Separation of Concerns

ASP.NET Core encourages separation of concerns through its modular architecture. Each component (e.g., controllers, services, models) should have a single responsibility:

- **Controllers**: Handle HTTP requests and responses.
- **Services**: Implement business logic.
- **Models**: Represent data structures.

### 2. Dependency Injection

Dependency injection (DI) is a core feature in ASP.NET Core that promotes loose coupling between components. By injecting dependencies instead of creating them directly, developers can write more testable and maintainable code:

- Use DI to manage service lifetimes (`Transient`, `Scoped`, or `Singleton`).
- Avoid static classes for stateful operations.

### 3. Middleware Pipeline

The middleware pipeline is a sequence of handlers that process incoming requests before they reach the controller. It's crucial to order middleware correctly and avoid unnecessary layers:

- Place authentication middleware early in the pipeline.
- Use error-handling middleware at the end.

## Code Example

```csharp
// Example: Using Dependency Injection with Scoped Lifetime
public class OrderService : IOrderService
{
    private readonly ILogger<OrderService> _logger;
    private readonly IProductRepository _productRepo;

    public OrderService(ILogger<OrderService> logger, IProductRepository productRepo)
    {
        _logger = logger;
        _productRepo = productRepo;
    }

    public async Task<PlaceOrderResult> PlaceOrderAsync(Order order)
    {
        try
        {
            var products = await _productRepo.GetProductsByIds(order.ProductIds);
            // Process the order...
            return new PlaceOrderResult { Success = true };
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Failed to place order.");
            throw;
        }
    }
}

// Startup.cs or Program.cs
builder.Services.AddScoped<IOrderService, OrderService>();
```

### Explanation

- **Dependency Injection**: The `IProductRepository` and `ILogger<OrderService>` are injected into the constructor of `OrderService`. This makes the service testable and avoids tight coupling.
- **Scoped Lifetime**: Using a scoped lifetime ensures that each HTTP request gets its own instance of `OrderService`, which is ideal for stateful operations.

## Common "Gotchas"

1. **Overusing Middleware**:
   - Adding unnecessary middleware layers can slow down requests. Only include middleware that directly contributes to the application's functionality.
2. **Ignoring Validation**:
   - Failing to validate input data (e.g., using `ModelState.IsValid`) can lead to runtime errors or security vulnerabilities.
3. **Hardcoding Configuration**:
   - Avoid hardcoding configuration values in code. Use environment-specific configuration files and inject them into services as needed.

## Opinionated Advice

- **Follow the Single Responsibility Principle**: Each class should have a single responsibility.
- **Use Strongly-Typed HTTP Requests**: Instead of relying on `Request.Form` or `Request.QueryString`, use model binding to strongly type request data.
- **Implement Caching Strategically**: Use caching for expensive operations, but be mindful of cache invalidation and expiration policies.

## When Defaults Are Enough

- **Default Middleware Pipeline**: For most applications, the default middleware pipeline (e.g., exception handling, static file serving) is sufficient. Customizations should only be made when necessary.
- **Basic Authentication**: If your application doesn't require complex authentication schemes, using basic authentication or JWT tokens with minimal configuration can suffice.

## Boundaries of Overkill

- **Overengineering Middleware**:
  - Adding custom middleware for simple tasks (e.g., logging) can introduce unnecessary complexity. Use built-in features unless you have a specific requirement.
- **Excessive Validation Layers**:
  - While validation is important, adding multiple layers of validation (e.g., both in the controller and service layer) can lead to redundancy.

## Summary

ASP.NET Core best practices are essential for building maintainable, scalable, and efficient applications. By adhering to principles like separation of concerns, dependency injection, and proper middleware usage, developers can ensure their code is robust and easy to maintain.

### Take-home Value

1. **Separate Concerns**: Ensure each component has a single responsibility.
2. **Leverage Dependency Injection**: Use DI for managing dependencies and promoting testability.
3. **Order Middleware Correctly**: Place authentication and error-handling middleware in the appropriate order.
4. **Validate Input Data**: Always validate user input to prevent runtime errors or security issues.
5. **Use Environment-Specific Configuration**: Avoid hardcoding configuration values; use environment-specific files instead.
6. **Be Mindful of Overengineering**: Keep solutions simple unless there's a clear need for complexity.
