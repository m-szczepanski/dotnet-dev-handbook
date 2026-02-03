# 11: GC Modes & Configuration

## What problem does this solve?

Garbage Collection (GC) is a critical component of the .NET runtime that manages memory allocation and deallocation. Understanding how to configure GC modes can help developers optimize application performance, especially in scenarios with high throughput or specific resource constraints. This chapter focuses on practical awareness of GC configurations like **Workstation vs Server GC** and **Background GC**, as well as when default settings are sufficient.

## The Big Idea

Imagine your application is a busy restaurant kitchen. Garbage Collection is the team responsible for cleaning up after each meal preparation. In a small café, you might have one cleaner who pauses work to tidy up (Workstation GC). However, in a large commercial kitchen, multiple cleaners work continuously without interrupting cooking (Server GC). Similarly, Background GC ensures that cleanup happens smoothly while minimizing interruptions to the main workflow.

## Mechanics Relevant to Developers

### Workstation vs Server GC

- **Workstation GC**: Designed for single-threaded or low-concurrency applications. It pauses all threads during garbage collection, which can lead to noticeable delays ("stop-the-world" events). This mode is suitable for desktop apps where user experience takes precedence over throughput.
- **Server GC**: Optimized for multi-core servers and high-throughput scenarios. It uses multiple threads to perform garbage collection in parallel, reducing the impact on application performance. Server GC minimizes pauses by leveraging concurrency.

### Background GC

Background GC allows the runtime to perform garbage collection asynchronously without blocking the main thread. This is particularly useful in interactive applications where responsiveness is critical. However, it may introduce additional memory pressure due to concurrent operations.

## Code Example

Here's a simple example demonstrating how to configure GC settings programmatically:

```csharp
using System;

class Program
{
    static void Main()
    {
        // Set the garbage collection mode to Server GC (if supported)
        if (GCSettings.IsServerGCSupported)
        {
            GCSettings.LatencyMode = GCLatencyMode.SustainedHighLatency;
            Console.WriteLine("Server GC is enabled with high latency mode.");
        }
        else
        {
            Console.WriteLine("Workstation GC is in use.");
        }

        // Trigger a garbage collection manually (for demonstration purposes)
        GC.Collect();
    }
}
```

### Explanation of the Code

- **`GCSettings.IsServerGCSupported`**: Checks if Server GC is available on the current platform.
- **`GCLatencyMode.SustainedHighLatency`**: Configures the runtime to prioritize throughput over low-latency operations, suitable for server applications.

## Common "Gotchas"

1. **Assuming Defaults Are Always Optimal**:
   - The default GC mode (Workstation or Server) is determined by the platform and application type. Misconfiguring this can lead to unnecessary pauses in interactive apps or suboptimal performance in server environments.
2. **Over-Configuring for No Reason**:
   - Changing GC settings without understanding the specific needs of your application can introduce complexity without benefits. Always measure before optimizing.
3. **Ignoring Platform Constraints**:
   - Some platforms (e.g., mobile devices) may not support certain GC modes, leading to runtime errors if configured improperly.

## Opinionated Advice

- **Stick with Defaults Unless Necessary**: The .NET runtime is highly optimized, and default settings are usually sufficient for most applications.
- **Profile Before Configuring**: Use tools like PerfView or dotnet-trace to identify memory pressure points before tweaking GC settings. Don't optimize prematurely.
- **Understand Your Application's Needs**:
  - Interactive apps (e.g., desktop UI) benefit from Workstation GC with Background GC enabled for smooth user experience.
  - Server applications (e.g., web APIs, batch processing) should use Server GC to maximize throughput and minimize pauses.

## When Defaults Are Enough

- **Most Desktop Applications**: Unless your app performs heavy computations or manages large datasets, the default Workstation GC is likely sufficient.
- **Small-Scale Web Apps**: For low-to-moderate traffic web applications running on a single server, the default settings are often adequate without customization.

## Boundaries of Overkill

- **Overusing Background GC in Server Scenarios**:
  - While Background GC can reduce pauses, it may increase memory pressure and lead to more frequent garbage collections. In high-throughput servers, this could degrade performance.
- **Forcing Server GC on Single-Core Machines**:
  - Using Server GC on single-core machines might not provide any benefits since parallelism cannot be leveraged effectively.

## Summary

- Garbage Collection is crucial for managing memory in .NET applications.
- Workstation GC is suitable for desktop apps, while Server GC is optimized for multi-core servers and high-throughput scenarios.
- Background GC helps reduce pauses but may increase memory pressure.
- Defaults are usually sufficient; only configure GC when profiling reveals specific performance bottlenecks.
- Always consider the trade-offs between throughput and latency based on your application's requirements.

### Take-home Value

1. Understand the difference between Workstation and Server GC modes.
2. Use Background GC for interactive apps to minimize pauses.
3. Profile before configuring GC settings; defaults are often optimal.
4. Be cautious about over-configuring, as it can introduce unnecessary complexity or performance issues.
5. Choose GC mode based on your application's workload (e.g., throughput vs. latency).
6. Avoid forcing configurations that don't align with the platform or hardware capabilities.
