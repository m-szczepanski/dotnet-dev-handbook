# 08: Controllers vs. Minimal APIs

## What problem does this solve?

When building web applications with ASP.NET Core, developers often face the decision of whether to use traditional controllers or minimal APIs. This choice impacts code organization, maintainability, and developer productivity. Understanding the differences between these two approaches helps developers make informed decisions based on their project's requirements.

## The Big Idea

Think of your application as a restaurant. Controllers are like waiters who handle customer requests by coordinating with chefs (services) to prepare meals (responses). Minimal APIs, on the other hand, are more like self-service kiosks where customers can directly place orders without intermediaries. Both approaches serve the same purpose—serving food—but they cater to different preferences and scenarios.

## Mechanics Relevant to Developers

### Controllers

Controllers in ASP.NET Core follow a traditional MVC (Model-View-Controller) pattern:

- **Routing**: URLs are mapped to controller actions using attributes like `[HttpGet]`, `[HttpPost]`.
- **Action Methods**: Each method handles a specific HTTP request type.
- **Middleware Integration**: Controllers can leverage middleware for cross-cutting concerns like authentication or logging.

### Minimal APIs

Minimal APIs provide a streamlined approach:

- **Direct Mapping**: Routes are defined directly in the `Program.cs` file using extension methods like `.MapGet()`, `.MapPost()`.
- **Lightweight**: Focuses on HTTP requests and responses without the overhead of controllers.
- **Flexibility**: Ideal for microservices or RESTful APIs where simplicity is key.

### Key Differences

| Feature                | Controllers                              | Minimal APIs                     |
|------------------------|------------------------------------------|----------------------------------|
| **Complexity**         | More structured, suitable for large apps | Simpler and more lightweight     |
| **Code Organization**  | Separation of concerns (actions)         | Direct route-to-action mapping   |
| **Middleware Usage**   | Integrated into the pipeline             | Explicitly defined per endpoint  |

## Code Example

### Controllers Example

```csharp
// Traditional Controller approach
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly IProductService _productService;

    public ProductsController(IProductService productService)
    {
        _productService = productService;
    }

    [HttpGet("{id}")]
    public async Task<ActionResult<Product>> Get(int id)
    {
        var product = await _productService.GetByIdAsync(id);
        if (product == null) return NotFound();
        return Ok(product);
    }
}
```

### Explanation

- **Controller Class**: Defines a class (`ProductsController`) that inherits from `ControllerBase`.
- **Action Method**: The `[HttpGet]` attribute maps the `/api/products/{id}` route to the `Get` method.
- **Dependency Injection**: Uses an injected service (`IProductService`) for business logic.

### Minimal APIs Example

```csharp
// Minimal API approach in Program.cs
app.MapGet("/api/products/{id}", async (int id, IProductService productService) =>
{
    var product = await productService.GetByIdAsync(id);
    return product == null ? Results.NotFound() : Results.Ok(product);
});
```

### Explanation

- **Direct Mapping**: The `MapGet` method directly maps the route `/api/products/{id}` to an inline lambda function.
- **Parameter Binding**: Route parameters (`id`) and services (`IProductService`) are passed as arguments.
- **Response Handling**: Uses built-in result types like `Results.NotFound()` or `Results.Ok()`.

## Common "Gotchas"

1. **Overusing Minimal APIs for Complex Logic**:
   - While minimal APIs are great for simple endpoints, complex business logic should still be handled by services rather than inline code.
2. **Ignoring Middleware in Controllers**:
   - Developers might forget to configure middleware like authentication or validation when using controllers, leading to security vulnerabilities.
3. **Mixing Styles**:
   - Combining controllers and minimal APIs in the same project can lead to inconsistent patterns and confusion among team members.

## Opinionated Advice

- **Use Minimal APIs for Simple Endpoints**: Ideal for microservices or RESTful APIs where simplicity is key.
- **Prefer Controllers for Complex Applications**: When you need advanced features like model binding, validation, or complex routing.
- **Consistency Matters**: Choose one approach per project to maintain consistency and avoid confusion.

## When Defaults Are Enough

- **Minimal APIs** are sufficient when:
  - The API is simple with few endpoints.
  - There's no need for complex middleware pipelines.
  - Performance optimization isn't a critical concern.
- **Controllers** are enough when:
  - You're building a traditional web application or API with multiple features.
  - You require advanced routing and action filters.

## Boundaries of Overkill

- **Overusing Controllers for Simple APIs**: Adding the overhead of controllers when minimal APIs would suffice can lead to unnecessary complexity.
- **Using Minimal APIs for Complex Logic**: Inline code in minimal APIs can become hard to maintain if business logic grows beyond simple CRUD operations.

## Summary

Controllers and minimal APIs are two powerful tools in ASP.NET Core, each suited for different scenarios. Controllers offer a structured approach with built-in features like model binding and middleware integration, making them ideal for complex applications. Minimal APIs provide simplicity and flexibility, perfect for lightweight microservices or RESTful APIs. Developers should choose based on the project's requirements to balance complexity and maintainability.

### Take-home Value

1. **Controllers** are best for structured, feature-rich applications.
2. **Minimal APIs** are ideal for simple, performance-critical endpoints.
3. Use dependency injection consistently regardless of the approach.
4. Avoid mixing controllers and minimal APIs in the same project unless necessary.
5. Profile your application to determine if one approach offers better performance than the other.
6. Choose based on team familiarity and project complexity.
