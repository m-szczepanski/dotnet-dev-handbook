# 14: Authentication vs Authorization

## What problem does this solve?

In web applications, managing user access is critical. Developers often struggle with distinguishing between **authentication** (who you are) and **authorization** (what you're allowed to do). Without clear understanding or proper implementation, security vulnerabilities can arise, leading to unauthorized access or data breaches. This topic helps developers build secure ASP.NET Core applications by clarifying the differences and showing how to implement both effectively.

## The Big Idea

Imagine your application as a house with multiple rooms. **Authentication** is like the front door lock—it verifies who you are (e.g., do you have the key?). Once inside, **Authorization** acts as room-specific locks—determining which rooms you're allowed to enter based on your role or permissions.

## Mechanics Relevant to Developers

### Authentication

- **Purpose**: Verifies a user's identity.
- **How it works in ASP.NET Core**:
  - Uses middleware like `Identity` or third-party providers (e.g., Google, Facebook).
  - Stores tokens or sessions to track authenticated users across requests.
  
### Authorization

- **Purpose**: Controls what actions an authenticated user can perform.
- **How it works in ASP.NET Core**:
  - Implements policies based on roles, claims, or custom logic.
  - Uses attributes like `[Authorize]` or middleware to enforce access rules.

## Code Example

Here's a practical example of implementing both authentication and authorization in ASP.NET Core:

```csharp
// Startup.cs (or Program.cs for .NET 8+)
public void ConfigureServices(IServiceCollection services)
{
    // Add Authentication Middleware
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    })
    .AddCookie();

    // Add Authorization Policies
    services.AddAuthorization(options =>
    {
        options.AddPolicy("AdminOnly", policy => 
            policy.RequireRole("Administrator"));
    });
}

// Example Controller with Authentication and Authorization
[ApiController]
public class SecureController : ControllerBase
{
    [Authorize] // Requires authentication (any authenticated user)
    [HttpGet("/secure")]
    public IActionResult GetSecureData()
    {
        return Ok("This is secure data accessible to all logged-in users.");
    }

    [Authorize(Policy = "AdminOnly")] // Requires specific role ("Administrator")
    [HttpGet("/admin-only")]
    public IActionResult GetAdminData()
    {
        return Ok("This is admin-only data, restricted by policy.");
    }
}
```

### Explanation

- **Authentication**: The `AddCookie` method sets up cookie-based authentication. Once a user logs in, their identity is stored in a secure cookie.
- **Authorization**:
  - The `[Authorize]` attribute ensures only authenticated users can access the `/secure` endpoint.
  - The `[Authorize(Policy = "AdminOnly")]` attribute restricts access to users with the `"Administrator"` role. This separation ensures fine-grained control over who can perform specific actions.

## Common "Gotchas"

1. **Confusing Authentication and Authorization**:
   - Developers often mix up authentication (identity verification) with authorization (access control). Always remember: authenticate first, then authorize.
2. **Over-relying on `[Authorize]` without Policies**:
   - Using the default `[Authorize]` attribute only ensures that a user is authenticated but doesn't enforce specific roles or permissions. For granular access control, define and use policies.
3. **Ignoring Token Expiry or Session Management**:
   - Authentication tokens (e.g., cookies) must be properly managed to prevent unauthorized access after expiration. Ensure token lifetimes are reasonable and refresh mechanisms are in place if needed.
4. **Hardcoding Roles Instead of Using Policies**:
   - Avoid hardcoding role names directly into controllers. Use policies to encapsulate authorization logic, making it reusable and easier to maintain.

## Opinionated Advice

- **Always Separate Authentication from Authorization**: Treat them as distinct concerns. Authenticate users first, then authorize their actions.
- **Use Policies for Fine-Grained Control**: Define custom policies based on roles, claims, or complex logic instead of relying solely on `[Authorize]` attributes with hardcoded role names.
- **Implement Role-Based Access Control (RBAC)**: Use predefined roles (`Administrator`, `User`) to simplify permission management. Avoid creating too many fine-grained roles that become hard to manage.
- **Regularly Audit Policies**: As your application grows, ensure authorization policies remain up-to-date and secure. Regular audits help prevent access control issues.

## When Defaults Are Enough

- **Basic Authentication Scenarios**:
  - For simple applications where all authenticated users have the same permissions, default authentication middleware (e.g., cookies) may suffice without additional authorization logic.
- **Single Role Applications**:
  - If your application has only one role (e.g., "User"), you might not need complex policies. A basic `[Authorize]` attribute could be enough.

## Boundaries of Overkill

- **Over-engineering Policies**:
  - Creating overly complex authorization logic for simple applications can introduce unnecessary overhead and maintenance challenges.
- **Ignoring Authentication in Authorization Checks**
  - Always ensure that users are authenticated before applying authorization. Relying solely on `[Authorize]` without proper authentication setup leaves your application vulnerable.

## Summary

Authentication and authorization are fundamental to building secure ASP.NET Core applications. Authentication verifies a user's identity, while authorization controls what actions they can perform. By using middleware like `Identity`, defining policies, and implementing attributes like `[Authorize]`, developers can create robust security models that protect their applications from unauthorized access.

### Take-home Value

1. **Authentication** is about verifying who the user is (e.g., via login credentials).
2. **Authorization** determines what actions an authenticated user is allowed to perform.
3. Use policies for fine-grained authorization instead of hardcoding roles directly in controllers.
4. Always authenticate users before authorizing their actions.
5. Regularly audit and update your authentication and authorization logic as your application evolves.
6. Avoid over-engineering security by using defaults when appropriate, but don't compromise on critical scenarios.
