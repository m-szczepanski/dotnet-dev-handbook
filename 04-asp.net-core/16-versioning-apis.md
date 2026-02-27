# 16: Versioning APIs

## What problem does this solve?

Versioning APIs is essential when you need to evolve your API over time without breaking existing clients. As your application grows, new features are added, and old ones might be deprecated or removed. Without proper versioning, updating the API could lead to compatibility issues for clients that rely on older versions. This problem becomes especially critical in microservices architectures where multiple teams may depend on a single API.

## The Big Idea

Think of your API as a public contract between you (the provider) and other developers or systems (the consumers). Over time, this contract might need to change—perhaps because new features are added, existing ones are modified, or deprecated functionality is removed. Versioning is like adding version numbers to these contracts so that different clients can use the version they're comfortable with without being forced into an upgrade.

## Mechanics Relevant to Developers

API versioning in ASP.NET Core can be implemented at multiple levels:

- **URI-Based Versioning**: Adding the version number directly in the URL path (e.g., `/api/v1/users`).
- **Header-Based Versioning**: Using HTTP headers like `Accept-Version: 2.0`.
- **Query String Parameters**: Including a query parameter to specify the version (e.g., `/api/users?version=3`).

The most common and recommended approach is URI-based versioning because it's clear, easy to implement, and aligns well with REST principles.

### Key Considerations

1. **Backward Compatibility**: Ensure that new versions don't break existing clients unless absolutely necessary.
2. **Deprecation Policies**: Provide a deprecation period for old API versions before removing them entirely.
3. **Versioning Strategy**: Decide whether to use major/minor versioning (e.g., v1, v2) or semantic versioning.

## Code Example

Here's an example of how you can implement URI-based versioning in ASP.NET Core:

```csharp
using Microsoft.AspNetCore.Mvc;

namespace ApiVersioningExample.Controllers.v1
{
    [ApiController]
    [Route("api/v1/[controller]")]
    public class UsersController : ControllerBase
    {
        [HttpGet]
        public IActionResult GetUsers()
        {
            // Simulate fetching users from a database or service.
            var users = new List<string> { "Alice", "Bob" };
            return Ok(users);
        }
    }
}

namespace ApiVersioningExample.Controllers.v2
{
    [ApiController]
    [Route("api/v2/[controller]")]
    public class UsersController : ControllerBase
    {
        [HttpGet]
        public IActionResult GetUsers()
        {
            // Simulate fetching enhanced user data.
            var users = new List<User>
            {
                new User { Id = 1, Name = "Alice", Email = "alice@example.com" },
                new User { Id = 2, Name = "Bob", Email = "bob@example.com" }
            };
            return Ok(users);
        }

        public class User
        {
            public int Id { get; set; }
            public string Name { get; set; } = null!;
            public string Email { get; set; } = null!;
        }
    }
}
```

### Explanation

- **URI-Based Versioning**: Each version of the API is placed in a separate namespace (`v1` and `v2`) with distinct route prefixes.
- **Backward Compatibility**: The `/api/v1/users` endpoint returns basic user data, while `/api/v2/users` provides enhanced details like email addresses. Existing clients can continue using v1 without disruption.
- **Scalability**: As new versions are added (e.g., `v3`, `v4`), they can be organized similarly in their own namespaces and routes.

## Common "Gotchas"

1. **Overcomplicating Versioning**: Avoid creating too many versions unnecessarily. Each version increases maintenance overhead.
2. **Ignoring Deprecation Policies**: Failing to provide a deprecation period for old API versions can lead to sudden breaking changes, frustrating clients.
3. **Version Drift**: Without clear guidelines, developers might implement features in the wrong version, leading to confusion and inconsistencies.

## Opinionated Advice

- **Use URI-Based Versioning**: It's straightforward, aligns with REST principles, and is widely supported by tools and frameworks.
- **Deprecate Gradually**: Always provide a deprecation period (e.g., 6 months) before removing old API versions. Communicate this clearly in your documentation.
- **Document Changes**: Maintain detailed changelogs for each version to help clients understand what has changed between releases.

## When Defaults Are Enough

- **Simple APIs with Minimal Changes**: If your API is unlikely to change significantly, URI-based versioning might be sufficient without additional complexity like header-based or query parameter approaches.
- **Small Teams**: For smaller projects where the risk of breaking changes is low, basic versioning strategies may suffice.

## Boundaries of Overkill

- **Overengineering Versioning**: Adding complex version negotiation mechanisms (e.g., multiple headers and query parameters) when URI-based versioning would suffice.
- **Excessive Versions**: Maintaining too many versions simultaneously can lead to unnecessary complexity. Aim for a maximum of 2–3 active versions at any given time.

## Summary

Versioning APIs is crucial for managing changes over time without disrupting existing clients. By using URI-based versioning and adhering to best practices like backward compatibility and deprecation policies, you can ensure that your API evolves smoothly while maintaining trust with its users.

### Take-home Value

1. **Use URI-Based Versioning**: It's simple, clear, and aligns well with REST principles.
2. **Provide Deprecation Periods**: Always give clients time to migrate before removing old versions.
3. **Document Changes Thoroughly**: Maintain detailed changelogs for each version.
4. **Limit Active Versions**: Aim to maintain no more than 2–3 active API versions at a time.
5. **Avoid Overcomplicating**: Don't introduce unnecessary complexity unless required by your use case.
6. **Focus on Backward Compatibility**: Ensure new versions don't break existing clients unless absolutely necessary.
