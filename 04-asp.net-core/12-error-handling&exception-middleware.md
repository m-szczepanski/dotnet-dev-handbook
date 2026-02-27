# 12: Error Handling & Exception Middleware

## What problem does this solve?

In web applications, errors can occur at any stage—whether it's invalid user input, database issues, or unexpected server behavior. Without proper error handling and middleware to manage these exceptions, users might encounter cryptic error messages, while developers struggle to diagnose the root cause of problems. This topic addresses how ASP.NET Core provides robust mechanisms for catching, logging, and responding to errors in a way that is both user-friendly and developer-centric.

## The Big Idea

Imagine your web application as a relay race where each middleware component passes control to the next until it reaches the final destination (the endpoint). However, if one of the runners trips (an error occurs), you need a system to catch them before they fall completely out of the race. Exception handling and middleware in ASP.NET Core act like safety nets—catching errors gracefully, logging what went wrong, and ensuring users see meaningful responses instead of crashing.

## Mechanics Relevant to Developers

### Middleware Pipeline

In ASP.NET Core, middleware forms a pipeline where each component processes requests before passing them along or responding directly. Exception handling is integrated into this pipeline using **exception handlers**—specialized middleware that catches unhandled exceptions and provides appropriate fallbacks.

### How It Works

- **Exception Filters**: These are attributes applied to controllers or actions that catch specific types of exceptions.
- **Middleware for Global Error Handling**: Middleware like `UseExceptionHandler` or custom error-handling middleware can be added to the pipeline to handle errors globally across the application.
- **Status Codes and Responses**: Depending on the type of exception, middleware can return appropriate HTTP status codes (e.g., 400 for bad requests, 500 for server errors) along with user-friendly messages.

### Key Concepts

- **Middleware Order Matters**: The placement of error-handling middleware in the pipeline is critical. It should generally be added after other middleware that might throw exceptions but before any terminal middleware.
- **Logging Integration**: Error handling often integrates with logging frameworks like Serilog or NLog to capture detailed information about what went wrong.

## Code Example

### Example: Adding Global Exception Handling Middleware

```csharp
// Startup.cs or Program.cs (ASP.NET Core 8+)
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers()
    .AddJsonOptions(options =>
        options.JsonSerializerOptions.WriteIndented = true); // Optional: Pretty-print JSON

var app = builder.Build();

app.UseExceptionHandler(errorApp => 
{
    errorApp.Run(async context =>
    {
        var exceptionHandlerPathFeature =
            context.Features.Get<IExceptionHandlerPathFeature>();

        if (exceptionHandlerPathFeature != null)
        {
            var exception = exceptionHandlerPathFeature.Error;
            var problemDetails = new ProblemDetails
            {
                Status = StatusCodes.Status500InternalServerError,
                Title = "An unexpected error occurred.",
                Detail = "An unexpected error occurred. Please try again later.", // In production, log 'exception' details on the server instead of returning them to the client.
                Type = "https://tools.ietf.org/html/rfc7231#section-6.6.1"
            };

            context.Response.StatusCode = problemDetails.Status.Value;
            context.Response.ContentType = "application/problem+json";
            await context.Response.WriteAsJsonAsync(problemDetails);
        }
    });
});

app.MapControllers();

app.Run();
```

### Explanation

- **`UseExceptionHandler`**: This middleware catches unhandled exceptions and runs the provided delegate to handle them.
- **Problem Details**: The `ProblemDetails` class is used to provide a standardized JSON response that includes status codes, titles, and details about the error. In production, you should avoid exposing detailed exception messages (`exception.Message`) directly to users for security reasons.
- **Middleware Placement**: Placing this middleware early in the pipeline ensures it catches any exceptions thrown by routing, authentication, controllers, or other middleware.

## Common "Gotchas"

1. **Not Logging Exceptions Properly**:
   - Simply catching an exception and returning a response without logging can lead to undiagnosed issues in production.
   - Always integrate with a logging framework to capture detailed error information, including stack traces and request details.
2. **Returning Sensitive Information**:
   - Exposing internal exception messages (e.g., `exception.Message`) directly to users can reveal sensitive information about your application's internals, potentially leading to security vulnerabilities.
3. **Overriding Specific Error Codes**:
   - Be cautious when overriding HTTP status codes for specific exceptions. For example, returning a 200 OK response for an error might confuse clients or monitoring tools.
4. **Middleware Order**:
   - Exception-handling middleware should be registered early in the pipeline (before routing and authentication) so it can catch exceptions from all downstream middleware and endpoints. Placing it too late can prevent it from catching exceptions thrown by earlier middleware.

## Opinionated Advice

- **Always Log Exceptions**: Use a logging framework (e.g., Serilog, NLog) to capture detailed error information, including stack traces and request details.
- **Use Problem Details for Responses**: Return standardized JSON responses using the `ProblemDetails` class to ensure consistency across different types of errors.
- **Avoid Swallowing Exceptions Silently**: Catching exceptions without logging or rethrowing them can hide critical issues. Always log before handling or returning a response.
- **Customize Error Messages for Users**: Provide user-friendly error messages that explain what went wrong in plain language, rather than technical jargon.

## When Defaults Are Enough

- **Basic Exception Handling**: For simple applications where detailed error logging isn't critical, the default `UseExceptionHandler` middleware with minimal customization might suffice.
- **Development Environments**: In non-production environments (e.g., local development), you can enable more verbose exception details to aid debugging without worrying about security implications.

## Boundaries of Overkill

- **Overly Complex Error Responses**: Adding excessive logic or custom formatting for error responses can introduce unnecessary complexity. Keep it simple unless there are specific requirements.
- **Custom Middleware for Simple Cases**: For straightforward scenarios, relying on built-in middleware like `UseExceptionHandler` is often sufficient—custom middleware should be reserved for highly specialized use cases.

## Summary

Error handling in ASP.NET Core involves integrating exception-handling middleware into the request pipeline to catch and respond to errors gracefully. By using tools like `ProblemDetails`, developers can provide standardized, user-friendly responses while ensuring detailed logging for debugging purposes. Proper placement of middleware and adherence to security best practices are critical to building robust applications.

### Take-home Value

1. **Use Middleware Wisely**: Integrate exception-handling middleware into the pipeline after routing but before terminal components.
2. **Log Everything**: Always log exceptions with a proper logging framework, even if you return generic error messages to users.
3. **Standardize Responses**: Use `ProblemDetails` for consistent JSON responses that include status codes and user-friendly messages.
4. **Avoid Sensitive Information**: Do not expose internal exception details directly to users in production environments.
5. **Test Error Scenarios**: Ensure your application handles expected and unexpected errors gracefully through thorough testing.
6. **Keep It Simple**: Don't over-engineer error handling unless there are specific requirements that necessitate complexity.
