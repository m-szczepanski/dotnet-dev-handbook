# 19. File I/O and Basic System Interaction

## What problem does this solve?

File input/output (I/O) is a fundamental aspect of software development that allows applications to read from, write to, or interact with files on disk. Whether you're reading configuration data, saving user preferences, or processing large datasets, understanding how to work with files efficiently and safely is crucial.

This subchapter aims to provide a practical foundation for handling file operations in .NET, including asynchronous I/O (for performance) and common pitfalls to avoid.

## Core concept

File I/O involves interacting with the operating system's filesystem to read or write data. Think of it like opening a book (file), reading its contents, writing new information, or closing it when you're done. Asynchronous operations allow your application to perform other tasks while waiting for file operations to complete, improving responsiveness and performance.

## How it works

### Reading/Writing Files

- **Synchronous I/O**: Reads/writes files in a blocking manner—your code waits until the operation completes.
- **Asynchronous I/O**: Uses async/await to allow your application to continue executing other tasks while waiting for file operations to complete.

### Common APIs

- `System.IO.File`: Provides static methods for common file operations (e.g., reading/writing entire files).
- `StreamReader` and `StreamWriter`: Used for sequential access to text-based files.
- `FileStream`: Low-level access to binary data, allowing precise control over reads and writes.

## Practical examples

Here’s how you can read/write files synchronously and asynchronously:

### Synchronous File Reading/Writing

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        // Writing to a file (synchronously)
        string filePath = "example.txt";
        using (StreamWriter writer = new StreamWriter(filePath))
        {
            writer.WriteLine("Hello, World!");
        }

        // Reading from a file (synchronously)
        using (StreamReader reader = new StreamReader(filePath))
        {
            Console.WriteLine(reader.ReadToEnd());
        }
    }
}
```

### Asynchronous File Reading/Writing

```csharp
using System;
using System.IO;
using System.Threading.Tasks;

class Program
{
    static async Task Main()
    {
        string filePath = "example.txt";

        // Writing to a file (asynchronously)
        using (StreamWriter writer = new StreamWriter(filePath))
        {
            await writer.WriteLineAsync("Hello, Async World!");
        }

        // Reading from a file (asynchronously)
        using (StreamReader reader = new StreamReader(filePath))
        {
            string content = await reader.ReadToEndAsync();
            Console.WriteLine(content);
        }
    }
}
```

### Explanation

- **Synchronous**: Blocks the thread until the operation completes. Suitable for small files or when you don't need to perform other tasks while waiting.
- **Asynchronous**: Uses `async/await` to allow your application to continue executing other code while I/O operations are in progress, improving responsiveness.

## Common mistakes & pitfalls

1. **Forgetting to Dispose Resources**:
   - Always use `using` statements or dispose of file streams explicitly to release system resources.
2. **Blocking the UI Thread**:
   - Performing synchronous I/O on the UI thread can freeze your application (e.g., in desktop apps). Use asynchronous operations for better responsiveness.
3. **Ignoring Exceptions**:
   - File operations can fail due to permissions, disk errors, or other issues. Always handle exceptions appropriately to provide meaningful feedback.
4. **Buffering Issues**:
   - Writing small amounts of data frequently without flushing the buffer can lead to performance degradation. Use `Flush` when necessary.
5. **Path Handling**:
   - Hardcoding file paths can cause issues if your application is deployed on a different system or environment. Use relative paths or platform-specific path handling methods (e.g., `Path.Combine`).

## Best practices & recommendations

- **Use Async I/O for Performance**: For most applications, especially those with UI threads or performance-sensitive operations, prefer asynchronous file access using `async/await`.
- **Always Dispose Streams**: Wrap streams in `using` statements to ensure proper resource cleanup.
- **Handle Exceptions Gracefully**: Use try-catch blocks to handle potential errors like file not found, permission issues, or disk full exceptions.
- **Use Path.Combine for Safety**: Construct paths dynamically using `Path.Combine` to avoid issues with different operating systems (e.g., Windows vs. Linux).
- **Avoid Hardcoding Paths**: Use configuration files or environment-specific settings for file locations.

## When NOT to use this

While file I/O is essential, there are scenarios where alternative approaches might be more appropriate:

- **In-Memory Operations**: For small datasets that fit in memory, consider using `MemoryStream` instead of disk-based operations.
- **Database Storage**: If you need persistent storage for structured data, databases (e.g., SQL Server or NoSQL) may be a better choice than file systems.

## Summary

- File I/O allows applications to read from and write to files on disk.
- Synchronous I/O blocks execution until the operation completes, while asynchronous I/O uses `async/await` for improved performance and responsiveness.
- Always dispose of streams using `using` statements to release system resources.
- Handle exceptions gracefully to provide meaningful feedback in case of errors.
- Use relative paths or `Path.Combine` for platform-independent path handling.
