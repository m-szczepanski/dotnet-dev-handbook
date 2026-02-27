# 09: ASP.NET Core - Routing

## What problem does this solve?

Routing in ASP.NET Core addresses the challenge of mapping incoming HTTP requests to specific actions or controllers within an application. Without proper routing, developers would need to manually parse URLs and determine which part of their code should handle each request. This manual approach is error-prone, repetitive, and makes it difficult to maintain a clean URL structure for users.

## The Big Idea

Think of your web application as a city with streets and addresses. Routing acts like the street signs that direct incoming traffic (HTTP requests) to the correct destination (actions or controllers). Just as you wouldn't want to manually route every car in a city, routing automates this process by defining rules that map URLs to specific parts of your code.

## Mechanics Relevant to Developers

### Core Components of Routing

- **Endpoints**: These are the destinations where requests are routed. In ASP.NET Core, endpoints can be controllers or minimal APIs.
- **Route Templates**: Patterns used to match incoming URLs (e.g., `/api/products/{id}`).
- **Parameter Binding**: Extracting values from the URL and binding them to method parameters.

### How Routing Works

1. When a request comes in, ASP.NET Core evaluates route templates defined in your application.
2. If a template matches the incoming URL, the corresponding endpoint is invoked.
3. Parameters are extracted from the URL and passed to the action or controller as arguments.

### Middleware Integration

Routing is part of the middleware pipeline in ASP.NET Core. It sits between request handling and response generation, ensuring that requests reach the correct handler efficiently.

## Code Example

```csharp
// Define a simple route for an API endpoint
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    [HttpGet("{id}")]
    public ActionResult<Product> GetProduct(int id)
    {
        // Simulate fetching product by ID
        var product = ProductService.Get(id);
        if (product == null) return NotFound();
        return Ok(product);
    }
}

// Minimal API Example
app.MapGet("/products/{id}", async (int id, ProductRepository repo) =>
{
    var product = await repo.GetByIdAsync(id);
    return product is not null ? Results.Ok(product) : Results.NotFound();
});
```

### Explanation

- **Route Attribute**: The `[Route("api/[controller]")]` attribute dynamically generates the base route for the controller based on its name. For example, `ProductsController` becomes `/api/products`.
- **Parameter Binding**: The `{id}` in the URL is automatically bound to the `int id` parameter of the `GetProduct` method.
- **Minimal API Example**: Demonstrates how routing works with minimal APIs, where routes are defined directly on endpoints.

## Common "Gotchas"

1. **Ambiguous Routes**:
   - If multiple route templates match a single URL, ASP.NET Core may throw an exception or choose one arbitrarily. Always ensure that your routes are unique and non-overlapping.
2. **Case Sensitivity**:
   - By default, routing is case-insensitive in ASP.NET Core. This can lead to unexpected behavior if you rely on case-sensitive URLs.
3. **Route Order Matters**:
   - Routes are evaluated in the order they are defined. More specific routes should be placed before more general ones to avoid conflicts.
4. **Missing Parameters**:
   - If a route template expects parameters (e.g., `{id}`), but the incoming URL doesn't provide them, ASP.NET Core will not match the route unless you explicitly allow optional parameters.

## Opinionated Advice

- **Use Attribute Routing for Controllers**: For controller-based APIs, attribute routing (`[Route]`) provides better clarity and maintainability.
- **Prefer Minimal APIs for Simple Endpoints**: When building RESTful APIs, minimal APIs offer a cleaner and more concise way to define routes without the overhead of controllers.
- **Validate Route Parameters**: Always validate incoming parameters (e.g., `id` must be positive) to prevent invalid requests from reaching your business logic.

## When Defaults Are Enough

- **Basic CRUD Operations**: For simple RESTful APIs with standard endpoints like `/api/products/{id}`, default routing is sufficient without additional customization.
- **Minimal APIs for Simple Scenarios**: If you're building a straightforward API with no complex URL patterns, the built-in minimal API routing works well.

## Boundaries of Overkill

- **Overly Complex Route Templates**:
  - Avoid overly complicated route templates that require deep nesting or multiple parameters. This can lead to maintenance issues and make debugging difficult.

- **Custom Middleware for Simple Routing**:
  - Don't introduce custom middleware just to handle routing unless you have a specific, non-standard requirement. ASP.NET Core's built-in routing is highly flexible.

## Summary

Routing in ASP.NET Core simplifies the process of mapping HTTP requests to application logic by providing a declarative way to define URL patterns and their corresponding handlers. Understanding how routes are matched, parameter binding works, and common pitfalls can help developers build robust and maintainable web applications.

### Take-home Value

1. **Routes Map URLs to Actions**: Use route templates to match incoming URLs to specific endpoints.
2. **Parameter Binding is Automatic**: Extract URL parameters automatically using placeholders like `{id}`.
3. **Order Matters**: Define more specific routes before general ones to avoid conflicts.
4. **Minimal APIs are Concise**: Prefer minimal APIs for simple, RESTful endpoints.
5. **Validate Inputs**: Always validate route parameters to prevent invalid requests.
6. **Keep It Simple**: Avoid overly complex routing unless absolutely necessary.
