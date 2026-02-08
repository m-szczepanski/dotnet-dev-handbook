# 01: What is ASP.NET Core (and what it is not)

## What problem does this solve?

ASP.NET Core addresses the limitations of its predecessor, classic ASP.NET, by providing a modern, modular, and cross-platform framework for building web applications. It solves problems such as platform lock-in (being tied to Windows), lack of modularity in older versions, and inefficiencies in deployment models like IIS. By offering flexibility, performance improvements, and compatibility with cloud-native architectures, ASP.NET Core empowers developers to build scalable, resilient, and maintainable applications.

## The Big Idea

Imagine you're building a house. Classic ASP.NET was like constructing it entirely on concrete foundations—solid but rigid, limited to specific environments (Windows). ASP.NET Core is like using prefabricated modular components that can be assembled anywhere (cross-platform) and tailored for different needs (modularity). It’s designed to adapt to modern requirements while still providing the robustness of its predecessor.

## Mechanics Relevant to Developers

### Key Features of ASP.NET Core

- **Cross-Platform**: Runs on Windows, macOS, and Linux, allowing developers to build applications that can run anywhere.
- **Modular Design**: Built with a modular architecture where only required components are loaded into memory, reducing overhead.
- **Cloud-Native**: Optimized for containerization (Docker) and deployment in cloud environments like Azure or AWS.
- **Open Source**: Developed openly on GitHub, allowing the community to contribute and influence its direction.

### Runtime Behavior

ASP.NET Core applications run on top of .NET runtime (CoreCLR), which is optimized for performance. The modular design ensures that only necessary components are loaded into memory, reducing startup time and resource usage compared to classic ASP.NET.

## Code Example

Here’s a simple example of an ASP.NET Core web API:

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Startup
{
    // This method configures the services used by the application.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();
    }

    // This method configures the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
}
```

### Explanation

- **`Startup` Class**: The entry point for configuring services and the HTTP pipeline.
- **`ConfigureServices` Method**: Registers dependencies, such as controllers (`AddControllers`).
- **`Configure` Method**: Sets up middleware (e.g., routing) and defines how requests are handled.

## Common "Gotchas"

1. **Confusing ASP.NET Core with Classic ASP.NET**:
   - Developers new to ASP.NET Core might assume it’s just an updated version of classic ASP.NET, but its architecture is fundamentally different.
2. **Ignoring Cross-Platform Capabilities**:
   - While ASP.NET Core can run on multiple platforms, developers sometimes overlook the benefits and opportunities this provides for deployment flexibility.
3. **Not Leveraging Modular Design**:
   - Developers might load unnecessary components into their applications, leading to increased memory usage or slower startup times.

## Opinionated Advice

- **Embrace Cross-Platform Development**: Leverage ASP.NET Core’s ability to run on multiple platforms to future-proof your application.
- **Keep It Modular**: Use the modular design effectively by only including necessary dependencies in your project.
- **Optimize for Cloud-Native Environments**: Design applications with containerization and cloud deployment in mind from the start.

## When Defaults Are Enough

- **Basic Web APIs**: For simple RESTful services, the default configurations provided by ASP.NET Core (e.g., routing, controllers) are often sufficient without additional customization.
- **Development Environment**: In early stages of development, using the built-in developer tools and middleware is enough to get started quickly.

## Boundaries of Overkill

- **Overcustomizing Middleware**: Adding unnecessary or overly complex middleware can introduce performance bottlenecks. Stick to defaults unless you have a specific requirement.
- **Ignoring Platform-Specific Features**: While ASP.NET Core is cross-platform, some features (e.g., Windows-specific APIs) might still be relevant depending on your target environment.

## Summary

ASP.NET Core is a modern, modular, and cross-platform framework designed to address the limitations of classic ASP.NET. Its flexibility, performance optimizations, and cloud-native capabilities make it ideal for building scalable web applications in today’s distributed environments.

### Take-home Value

1. **Cross-Platform**: ASP.NET Core runs on Windows, macOS, and Linux.
2. **Modular Design**: Only required components are loaded into memory, reducing overhead.
3. **Cloud-Native**: Optimized for containerization and cloud deployment.
4. **Open Source**: Community-driven development ensures continuous improvement.
5. **Not Classic ASP.NET**: It’s a fundamentally different framework with modern architecture.
6. **Focus on Simplicity**: Leverage defaults unless you have specific requirements.
