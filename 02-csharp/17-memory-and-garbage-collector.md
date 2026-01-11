# 17. Memory Basics & Garbage Collector

## What problem does this solve?

Memory management is a critical aspect of software development that can significantly impact application performance and stability. Without proper understanding, developers may inadvertently create memory leaks or inefficient code, leading to issues like slow response times, high resource usage, or even crashes. This subchapter aims to provide a clear overview of how .NET handles memory allocation, object lifetimes, and garbage collection—empowering you to write more efficient and reliable applications.

## Core concept

Imagine your application as a house with many rooms (objects). Each room needs space (memory) to exist, but when the occupants leave (objects are no longer needed), they must be cleaned up or removed. The Garbage Collector (GC) is like a cleaning crew that automatically tidies up unused spaces without you having to manually clean every room. This automation saves time and reduces errors compared to manual memory management.

## How it works

In .NET, when your application creates an object, the runtime allocates memory for it on the managed heap. The GC monitors these objects and identifies which ones are no longer in use (i.e., not referenced by any part of the program). When the GC determines that an object is unreachable, it marks it as garbage and frees up its memory during a collection cycle.

### Key points

- **Allocation**: Objects are created on the managed heap.
- **Lifetime**: An object's lifetime ends when no references to it exist in your code.
- **GC Pauses**: The GC runs periodically (or triggered by low memory) to clean up unused objects, which can cause brief pauses in application execution.

## Practical examples

Here’s a simple example demonstrating how the GC works:

```csharp
using System;

class Program
{
    static void Main()
    {
        // Create an object and reference it
        var myObject = new MyClass();
        
        Console.WriteLine("Object created. Reference exists.");
        
        // Remove all references to the object
        myObject = null;
        
        GC.Collect(); // Trigger a garbage collection (for demonstration purposes)
        
        Console.WriteLine("Reference removed. Object is eligible for cleanup.");
    }
}

class MyClass
{
    public MyClass()
    {
        Console.WriteLine("MyClass instance created.");
    }

    ~MyClass() // Finalizer, called by the GC when cleaning up
    {
        Console.WriteLine("MyClass instance cleaned up by GC.");
    }
}
```

### Explanation

- When `myObject` is set to `null`, it becomes eligible for garbage collection.
- The `GC.Collect()` method forces a cleanup (not recommended in production, as the GC runs automatically).
- The finalizer (`~MyClass`) is called during cleanup, demonstrating when an object's memory is reclaimed.

## Common mistakes & pitfalls

1. **Premature Garbage Collection**: Calling `GC.Collect()` manually can lead to unnecessary pauses and performance issues.
2. **Memory Leaks via Cycles**: Objects that reference each other (cyclic references) may not be collected even if they're no longer needed, leading to memory leaks.
3. **Ignoring Large Objects**: Large objects are stored in a separate heap (`Large Object Heap`) and can cause fragmentation or slower cleanup.

## Best practices & recommendations

- **Avoid Manual GC Calls**: Let the runtime manage garbage collection automatically unless you have a specific performance profiling reason.
- **Use `using` Statements**: For disposable resources (e.g., files, database connections), use `using` to ensure proper cleanup.
- **Minimize Cyclic References**: Be mindful of object references that create cycles, as they can prevent objects from being collected.
- **Profile Memory Usage**: Use tools like Visual Studio's Diagnostic Tools or dotMemory to identify memory leaks and optimize usage.

## When NOT to use this

While the GC is powerful, it’s not a silver bullet. In scenarios where real-time performance is critical (e.g., game engines or financial trading systems), manual memory management might be necessary to avoid GC pauses. However, for most applications, relying on .NET's automatic garbage collection is both efficient and safe.

## Summary

- Memory allocation in .NET occurs on the managed heap.
- Objects are eligible for cleanup when no references exist.
- The Garbage Collector automatically manages memory but can cause brief pauses during cleanup.
- Avoid manual GC calls unless profiling indicates a need.
- Be mindful of cyclic references to prevent memory leaks.
- Use tools like `using` statements and profilers to optimize memory usage.
