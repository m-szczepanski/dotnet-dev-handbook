# 15: Working with HTTP Properly (Status Codes, Headers)

## What problem does this solve?

When building web applications, developers often need to interact with HTTP in a way that is both effective and compliant with standards. Misuse of status codes or improper handling of headers can lead to confusing behavior for clients, inconsistent API responses, or even security vulnerabilities. Understanding how to work with HTTP properly ensures that your application communicates clearly with browsers, APIs, and other services.

## The Big Idea

Think of HTTP as a language spoken between servers and clients. Status codes are like the tone of voice—indicating whether a request was successful (e.g., "200 OK") or encountered an issue (e.g., "404 Not Found"). Headers are like metadata tags that provide additional context, such as caching instructions ("Cache-Control"), authentication details ("Authorization"), or content type information ("Content-Type"). By mastering these elements, you can make your application more robust and user-friendly.

## Mechanics Relevant to Developers

### HTTP Status Codes

HTTP status codes are three-digit numbers grouped into categories:

- **2xx**: Success (e.g., `200 OK`, `201 Created`)
- **3xx**: Redirection (e.g., `301 Moved Permanently`, `304 Not Modified`)
- **4xx**: Client errors (e.g., `400 Bad Request`, `404 Not Found`)
- **5xx**: Server errors (e.g., `500 Internal Server Error`)

In ASP.NET Core, you can return these codes using the `StatusCode` or `StatusCodes` class. For example:

```csharp
return StatusCode(201); // 201 Created
```

### HTTP Headers

Headers provide metadata about requests and responses. Common headers include:

- **Request Headers**: Sent by clients to servers (e.g., `Accept`, `Authorization`)
- **Response Headers**: Sent by servers to clients (e.g., `Content-Type`, `Cache-Control`)

In ASP.NET Core, you can access or set headers using the `HttpContext.Request.Headers` and `HttpContext.Response.Headers` properties.

### Content Negotiation

ASP.NET Core automatically handles content negotiation based on request headers like `Accept`. This determines how data is serialized (e.g., JSON, XML) in responses.

## Code Example

Here’s an example of handling HTTP status codes and headers in ASP.NET Core:

```csharp
using Microsoft.AspNetCore.Mvc;

[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
        // Simulate database lookup
        var product = FindProductById(id);

        if (product == null)
        {
            return NotFound(); // 404 Not Found
        }

        // Add a custom header to the response
        Response.Headers.Append("X-Product-Availability", "In Stock");

        return Ok(product); // 200 OK with JSON content
    }
}
```

### Explanation

- **Status Code**: The `NotFound()` method automatically returns a `404 Not Found` status code when the product is not found.
- **Custom Header**: The `Response.Headers.Add` method adds a custom header (`X-Product-Availability`) to provide additional information about the product's availability.
- **Content Negotiation**: ASP.NET Core automatically serializes the `product` object as JSON (or another format based on the client’s `Accept` header).

## Common "Gotchas"

1. **Using Incorrect Status Codes**:
   - Misusing status codes can confuse clients. For example, returning a `200 OK` for an error scenario is misleading.
2. **Ignoring Headers**:
   - Failing to handle headers like `Cache-Control` or `Content-Type` can lead to inconsistent behavior across different clients.
3. **Overloading Custom Headers**:
   - Adding too many custom headers can make responses bloated and harder to maintain.
4. **Hardcoding Status Codes**:
   - Instead of using magic numbers (e.g., `return StatusCode(201);`), prefer named methods like `CreatedAtAction()` or `NotFound()` for better readability.

## Opinionated Advice

- **Use Named Methods**: Prefer named status code methods (`Ok()`, `NotFound()`) over raw status codes to make your intent clear.
- **Validate Headers**: Always validate request headers (e.g., authentication tokens) before processing requests.
- **Follow Standards**: Adhere to RFC standards for status codes and header usage to ensure compatibility with clients.
- **Minimize Custom Headers**: Use standard HTTP headers whenever possible. Avoid creating unnecessary custom headers unless absolutely required.

## When Defaults Are Enough

- **Basic Status Codes**: For most scenarios, the built-in status code methods (`Ok()`, `NotFound()`) are sufficient without needing to manually set codes.
- **Content-Type Negotiation**: ASP.NET Core’s automatic content negotiation handles most serialization needs without requiring manual intervention.

## Boundaries of Overkill

- **Overusing Custom Headers**: Adding too many custom headers can make your API harder to maintain and understand. Stick to standard HTTP conventions unless there's a compelling reason for customization.
- **Micro-Managing Status Codes**: While it’s important to use the right status codes, obsessing over edge cases (e.g., returning `204 No Content` instead of `200 OK`) can lead to unnecessary complexity.

## Summary

Working with HTTP properly involves understanding and correctly using status codes and headers. By leveraging ASP.NET Core’s built-in features for handling these elements, developers can create robust APIs that communicate effectively with clients while adhering to industry standards.

### Take-home Value

1. **Use Named Status Code Methods**: Prefer methods like `Ok()`, `NotFound()` over raw status code numbers.
2. **Validate Request Headers**: Always check and validate headers (e.g., authentication tokens) before processing requests.
3. **Follow HTTP Standards**: Adhere to RFC standards for status codes and header usage.
4. **Minimize Custom Headers**: Use standard HTTP headers whenever possible; avoid unnecessary customizations.
5. **Content Negotiation**: Rely on ASP.NET Core’s automatic content negotiation unless you have specific serialization requirements.
6. **Avoid Overcomplicating**: Stick to simple, effective solutions unless there's a clear need for advanced customization.
