# 07: Configuration & Environments

## What problem does this solve?

Managing application configuration across different environments (e.g., development, testing, production) is a common challenge for developers. Without proper handling, hardcoding settings or manually switching configurations can lead to errors, security risks, and maintenance headaches. This topic addresses how .NET provides robust mechanisms to manage configuration dynamically based on the environment.

## The Big Idea

Think of your application as a traveler with a backpack. Each destination (environment) requires different gear (settings). Instead of packing everything for every trip or manually swapping items at each stop, you use a smart system that automatically adapts your gear based on where you are. Similarly, .NET allows applications to load the right configuration dynamically depending on the environment they're running in.

## Mechanics Relevant to Developers

### Configuration Sources in .NET

.NET supports multiple sources for application configuration:

- **appsettings.json**: The primary configuration file.
- **Environment Variables**: Override settings at runtime (e.g., `ASPNETCORE_ENVIRONMENT`).
- **Command-Line Arguments**: Useful for ad-hoc overrides during startup.

### Environment-Specific Files

.NET automatically loads environment-specific files like `appsettings.Development.json`, `appsettings.Production.json`, etc. These files override or extend the base configuration in `appsettings.json`.

### Configuration Hierarchy

Configuration values are resolved using a hierarchy:

1. **Hardcoded settings** (lowest priority).
2. **Base file (`appsettings.json`)**.
3. **Environment-specific files** (e.g., `appsettings.Development.json`).
4. **Environment variables**.
5. **Command-line arguments** (highest priority).

### Configuration Providers

.NET supports various providers to load configuration from different sources:

- JSON, XML, INI, etc.
- External services like Azure Key Vault or AWS Secrets Manager.

#### Example: Using Environment Variables

```csharp
// Accessing configuration (e.g., from environment variables) in code
var configuration = builder.Configuration;
string connectionString = configuration.GetConnectionString("Default");
```

### Configuration Binding

.NET allows binding configuration values to strongly-typed objects, making it easier to work with complex settings:

```csharp
public class DatabaseSettings
{
    public string ConnectionString { get; set; }
    public int CommandTimeout { get; set; }
}
```

## Code Example

Here's a complete example of configuring an application for different environments using `appsettings.json` and environment-specific files.

### appsettings.json

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "ConnectionStrings": {
    "Default": "UseDevelopmentStorage=true"
  },
  "AppSettings": {
    "Environment": "Unknown"
  }
}
```

### appsettings.Development.json

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "Microsoft.AspNetCore": "Information"
    }
  },
  "ConnectionStrings": {
    "Default": "Server=localhost;Database=DevDB;Integrated Security=true;"
  },
  "AppSettings": {
    "Environment": "Development"
  }
}
```

### appsettings.Production.json

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "ConnectionStrings": {
    "Default": "Server=prod-db.example.com;Database=ProdDB;User Id=appuser;Password={your-password-here};"
    // IMPORTANT: Never commit secrets to source control. Use Azure Key Vault, environment variables, or user secrets instead.
  },
  "AppSettings": {
    "Environment": "Production"
  }
}
```

### Program.cs (Startup Configuration)

```csharp
var builder = WebApplication.CreateBuilder(args);

// Load configuration from appsettings.json and environment-specific files
builder.Configuration.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
string envName = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") ?? "Production";
builder.Configuration.AddJsonFile($"appsettings.{envName}.json", optional: true, reloadOnChange: true);

// Bind configuration to strongly-typed objects
var appSettings = builder.Configuration.GetSection("AppSettings").Get<AppSettings>();
builder.Services.AddSingleton(appSettings);

var app = builder.Build();
```

### Explanation

- **Base Configuration**: `appsettings.json` provides default settings.
- **Environment-Specific Overrides**: Environment-specific files like `appsettings.Development.json` override or extend the base configuration based on the current environment (`ASPNETCORE_ENVIRONMENT`).
- **Configuration Binding**: The `AppSettings` section is bound to a strongly-typed object, making it easier to work with in code.
- **Environment Detection**: The runtime automatically detects the environment using the `ASPNETCORE_ENVIRONMENT` variable.

## Common "Gotchas"

1. **Hardcoding Environment Variables**:
   - Avoid hardcoding environment-specific values (e.g., connection strings) directly into your code or configuration files. Use placeholders and let .NET resolve them dynamically.
2. **Ignoring ReloadOnChange**:
   - If you're working in a development environment, enable `reloadOnChange` to automatically reload configuration changes without restarting the app.
3. **Overriding with Environment Variables**:
   - Be cautious when using environment variables for sensitive data like passwords or connection strings. Ensure they are securely managed and not exposed accidentally.
4. **Missing Environment-Specific Files**:
   - If an environment-specific file is missing, .NET will fall back to the base configuration (`appsettings.json`). This can lead to unexpected behavior if you assume a specific setting exists in all environments.

## Opinionated Advice

- **Use Strongly-Typed Configuration**: Always bind configuration settings to strongly-typed objects. This reduces errors and makes your code more maintainable.
- **Centralize Environment Detection**: Use environment variables (`ASPNETCORE_ENVIRONMENT`) consistently across your application to ensure proper behavior in different environments.
- **Secure Sensitive Data**: Store sensitive information like connection strings or API keys in external stores (e.g., Azure Key Vault) and load them via configuration providers. Avoid hardcoding these values directly into your codebase.

## When Defaults Are Enough

- **Simple Applications with Minimal Configuration**:
  - For small applications that don't require complex configuration, the default `appsettings.json` file might be sufficient without needing environment-specific files or external stores.
- **Development Environments Without Sensitive Data**:
  - In development environments where sensitive data isn't exposed, using plain text in `appsettings.Development.json` can simplify setup.

## Boundaries of Overkill

- **Overusing External Stores for Simple Settings**:
  - For simple settings like logging levels or basic app configurations, external stores might add unnecessary complexity. Stick to `appsettings.json` unless you have a compelling reason to use an external provider.
- **Complex Hierarchies Without Clear Documentation**:
  - Overly complex configuration hierarchies (e.g., multiple layers of overrides) can make it difficult for developers to understand where settings are coming from. Keep the hierarchy simple and document each layer clearly.

## Summary

Configuration management in .NET is designed to be flexible and adaptable, allowing applications to load different settings based on their environment. By leveraging `appsettings.json`, environment-specific files, and external providers, developers can build robust applications that handle configuration dynamically without hardcoding or manual intervention.

### Take-home Value

1. **Use Environment-Specific Files**: Leverage `appsettings.{Environment}.json` for environment-specific overrides.
2. **Bind to Strongly-Typed Objects**: Always bind configuration settings to strongly-typed objects for better maintainability and type safety.
3. **Centralize Environment Detection**: Use the `ASPNETCORE_ENVIRONMENT` variable consistently across your application.
4. **Secure Sensitive Data**: Store sensitive information in external stores like Azure Key Vault instead of hardcoding it into configuration files.
5. **Keep It Simple When Possible**: Avoid over-engineering for simple applications or settings that don't require complex hierarchies.
6. **Document Configuration Sources**: Clearly document where and how configuration values are loaded to avoid confusion among team members.
