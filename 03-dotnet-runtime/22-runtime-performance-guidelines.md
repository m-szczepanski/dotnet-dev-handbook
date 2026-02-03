# 22: Runtime Performance Guidelines

## What problem does this solve?

Performance is a critical aspect of software development, and understanding how to optimize your application for both startup time and runtime efficiency can significantly impact user experience. However, many developers struggle with where to start or how to approach performance tuning without introducing unnecessary complexity. By following actionable guidelines, you'll be able to write code that performs well while maintaining readability and maintainability.

## The Big Idea

Think of optimizing your application like fine-tuning a race car: it's not about making every part perfect but rather identifying the bottlenecks and addressing them strategically. By focusing on allocation awareness, balancing startup vs. throughput needs, and measuring before tuning, you'll build applications that are both fast and efficient.

## Runtime Performance Guidelines

### 1. Allocation Awareness

**Why it matters:** Memory allocations in .NET can impact performance, especially when they occur frequently or involve large objects. Understanding how your code interacts with the garbage collector (GC) is essential for minimizing unnecessary work.

#### Key Points (Allocation Awareness)

- **Minimize short-lived object creation:** Creating and discarding many small objects forces the GC to run more often, which can introduce pauses.
- **Reuse memory when possible:** Instead of creating new instances repeatedly, consider reusing existing objects or using pooled resources.
- **Avoid boxing/unboxing primitives:** Boxing converts value types into reference types (e.g., `int` → `object`), which allocates memory on the heap.

#### Example (Allocation Awareness)

```csharp
// Good: Reusing a buffer instead of creating new arrays
byte[] buffer = new byte[1024];
for (int i = 0; i < 1000; i++)
{
    // Use buffer for operations
}

// Bad: Creating new arrays repeatedly
for (int i = 0; i < 1000; i++)
{
    var tempArray = new byte[1024];
}
```

**Explanation:** Reusing the `buffer` avoids repeated memory allocations, reducing pressure on the GC.

### 2. Startup vs Throughput

**Why it matters:** Different applications have different performance priorities. For example, a web server might prioritize throughput (processing requests quickly), while a desktop application might focus on fast startup time to provide a smooth user experience.

#### Key Points (Startup vs Throughput)

- **Startup optimization:** Focus on reducing the number of assemblies loaded at startup and minimizing JIT compilation overhead.
- **Throughput optimization:** Optimize for efficient memory usage, minimal allocations, and leveraging async/await for I/O-bound operations.
- **Profile your application's lifecycle:** Understand whether users are more sensitive to initial load time or ongoing performance.

#### Example (Startup vs Throughput)

```csharp
// Good: Lazy loading assemblies in a web app
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        // Load only necessary assemblies at startup
        services.AddControllers();
        services.AddSingleton<MyService>();
    }
}

// Bad: Loading all assemblies upfront, even unused ones
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();
        services.AddDbContext<MyDbContext>(); // Unnecessary if not used immediately
        services.AddIdentity(); // Only needed for authentication flows
    }
```

**Explanation:** By deferring the loading of assemblies and components that aren't immediately required, you can reduce startup time without compromising functionality.

### 3. Measuring Before Tuning

**Why it matters:** Performance tuning is often counterproductive if done blindly. Without concrete measurements, optimizations might not address actual bottlenecks or could even introduce new issues.

#### Key Points

- **Use profiling tools:** Tools like .NET's built-in `dotnet-trace`, `PerfView`, or third-party profilers can help identify hotspots in your code.
- **Benchmark changes:** Before and after making performance optimizations, measure the impact using benchmarks to ensure improvements are real.
- **Focus on critical paths:** Don't optimize everything; prioritize areas that have the most significant impact on user experience.

#### Example

```csharp
// Good: Benchmarking before optimization
using System.Diagnostics;

public class PerformanceTest
{
    public void MeasureStartupTime()
    {
        var sw = Stopwatch.StartNew();
        // Simulate application startup logic here
        sw.Stop();
        Console.WriteLine($"Startup time: {sw.ElapsedMilliseconds} ms");
    }
}

// Bad: Optimizing without measurement
// Making random changes to code without understanding their impact.
```

**Explanation:** Benchmarking ensures that optimizations are data-driven and focused on areas where they matter most.

## Common "Gotchas" to Avoid

1. **Over-optimizing prematurely:** Spending too much time optimizing code before identifying actual bottlenecks can lead to unnecessary complexity.
2. **Ignoring the impact of third-party libraries:** External dependencies can introduce performance issues; ensure you're using efficient versions and configurations.
3. **Focusing solely on GC without addressing other bottlenecks:** While memory management is important, don't overlook CPU-bound or I/O-bound operations that might be causing slowdowns.

## Opinionated Advice

- **Prioritize allocation awareness:** Minimizing unnecessary allocations is one of the most impactful ways to improve performance.
- **Balance startup and throughput based on your application's needs:** Don’t optimize for one at the expense of the other unless it’s explicitly required.
- **Always measure before tuning:** Use profiling tools and benchmarks to validate that optimizations are effective.

## When These Concepts Become Anti-Patterns

1. **Over-engineering allocation avoidance:** Avoiding allocations at all costs can lead to overly complex code that's harder to maintain.
2. **Neglecting user experience for throughput optimization:** Focusing solely on raw performance metrics without considering the impact on users (e.g., slow startup times) can alienate your audience.
3. **Blindly applying optimizations without data-driven insights:** Making changes based on assumptions rather than measurements can lead to wasted effort or even regressions.

## Summary

- **Allocation awareness** helps minimize GC pressure by reducing unnecessary memory allocations and reusing resources where possible.
- **Balancing startup vs throughput** ensures your application performs well in scenarios that matter most to users, whether it's fast initial load times or sustained high performance.
- **Measuring before tuning** prevents wasted effort on optimizations that don't address actual bottlenecks.
