# 13: Logging in ASP.NET Core

## What problem does this solve?

In modern applications, logging plays a critical role in debugging, monitoring, and maintaining system health. Without proper logging, developers often struggle to diagnose issues, especially in production environments where direct access is limited. Logging helps capture essential information about application behavior, errors, performance bottlenecks, and user interactions, enabling teams to react quickly to problems and improve the overall reliability of their systems.

## The Big Idea

Imagine your application as a diary that records its activities throughout the day. When something unexpected happens (like an error or slow response), you can go back and read the entries to understand what went wrong. Similarly, logging in ASP.NET Core allows developers to instrument their applications with detailed logs that provide insights into runtime behavior. These logs act as a "diary" for your application, helping you identify issues without needing direct access to the running system.

## Mechanics Relevant to Developers

ASP.NET Core provides a robust and extensible logging framework based on **Microsoft.Extensions.Logging**. This framework is designed to be flexible, allowing developers to integrate various log sinks (e.g., console, files, databases) while maintaining consistent formatting and filtering mechanisms.

### Key Components

- **ILogger**: The primary interface for writing logs.
- **Log Levels**: Define the severity of a log message (`Trace`, `Debug`, `Information`, `Warning`, `Error`, `Critical`).
- **Logger Providers**: Adapters that write logs to specific destinations (e.g., Console, File, Serilog, Seq).

### How It Works

1. Developers inject an `ILogger<T>` instance into their classes.
2. The framework resolves the logger based on configuration and writes log messages to registered providers.
3. Filters can be applied to control which logs are written or displayed.

## Code Example

```csharp
using Microsoft.Extensions.Logging;

public class OrderService
{
    private readonly ILogger<OrderService> _logger;

    public OrderService(ILogger<OrderService> logger)
    {
        _logger = logger;
    }

    public void ProcessOrder(int orderId)
    {
        try
        {
            // Simulate processing logic
            _logger.LogInformation("Processing order with ID: {OrderId}", orderId);
            var result = Process(orderId);

            if (result.IsSuccess)
            {
                _logger.LogDebug("Order processed successfully.");
            }
            else
            {
                _logger.LogWarning("Failed to process order. Error: {@ErrorDetails}", result.Error);
            }
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "An error occurred while processing the order.");
            throw;
        }
    }

    private Result Process(int orderId)
    {
        // Simulated business logic
        return new Result { IsSuccess = true };
    }
}

public class Result
{
    public bool IsSuccess { get; set; }
    public string Error { get; set; }
}
```

### Explanation

- **Dependency Injection**: The `ILogger<OrderService>` is injected into the constructor, ensuring proper separation of concerns.
- **Log Levels**:
  - `Information`: Used for general operational details (e.g., processing an order).
  - `Debug`: For low-level information that might be useful during development but not in production.
  - `Warning`/`Error`: Capture issues or failures with appropriate severity.
- **Structured Logging**: Using `{}` and `@{}` placeholders ensures logs are structured, making them easier to parse and analyze later.

## Common "Gotchas"

1. **Overusing Log Levels**:
   - Misusing log levels (e.g., logging critical business logic as `Debug`) can lead to noisy logs or missed critical issues.
2. **Missing Exception Details**:
   - Forcing developers to manually include exception details in log messages instead of using the built-in `{ex}` placeholder.
3. **Performance Overhead**:
   - Logging at high verbosity levels (e.g., `Debug`) can introduce performance overhead, especially if logs are written frequently or to slow sinks like databases.
4. **Ignoring Log Filters**:
   - Failing to configure log filters can result in excessive logging, overwhelming developers with irrelevant information.

## Opinionated Advice

- **Use Structured Logging**: Always use placeholders (`{}`) for dynamic values instead of string concatenation. This ensures logs are machine-readable and easier to analyze.
- **Log at the Right Level**:
  - Use `Information` for operational details, `Warning` for recoverable issues, and `Error/Critical` for non-recoverable failures.
- **Avoid Logging Sensitive Data**: Never log sensitive information (e.g., passwords, tokens) directly. Instead, use placeholders or redact sensitive fields.
- **Centralize Log Configuration**:
  - Configure logging settings in a centralized location (e.g., `appsettings.json`) to ensure consistency across the application.

## When Defaults Are Enough

- **Basic Console Logging**: For development environments, the default console logger is often sufficient. It provides immediate feedback without requiring additional setup.
- **Simple File Logging**: In staging or low-traffic production environments, writing logs to a file can be adequate for basic monitoring and debugging.

## Boundaries of Overkill

- **Overcomplicating Log Configuration**:
  - Adding multiple log providers (e.g., console, file, database) without clear use cases can introduce unnecessary complexity.
- **Excessive Logging**:
  - Logging every minor detail (e.g., method entry/exit points) can lead to bloated logs that are difficult to manage and analyze.

## Summary

Logging in ASP.NET Core is a powerful tool for monitoring application behavior, diagnosing issues, and ensuring system reliability. By leveraging the built-in `Microsoft.Extensions.Logging` framework, developers can instrument their applications with structured, filtered, and extensible logging capabilities. Proper use of log levels, structured placeholders, and centralized configuration ensures that logs are both actionable and performant.

### Take-home Value

1. **Use Structured Logging**: Always use `{}` placeholders for dynamic values to ensure machine-readable logs.
2. **Log at the Right Level**: Choose appropriate log levels (`Information`, `Warning`, `Error`) based on the severity of the event.
3. **Avoid Sensitive Data**: Never log sensitive information directly; redact or mask such data instead.
4. **Centralize Configuration**: Configure logging settings in a centralized location for consistency across the application.
5. **Monitor Log Volume**: Be mindful of excessive logging, as it can impact performance and make logs harder to manage.
6. **Use Filters Wisely**: Apply log filters to control verbosity and focus on relevant information.
