# 14: Collections and Common Data Structures

## What problem does this solve?

Collections are fundamental to solving real-world problems in software development. Whether you're managing a list of users, tracking inventory, or processing data from APIs, choosing the right collection type is crucial for efficiency, readability, and performance. Developers often struggle with selecting the appropriate data structure or understanding when certain operations might impact performance. This subchapter aims to provide practical guidance on using common .NET collections like `List<T>`, `Dictionary<TKey,TValue>`, and `IEnumerable<T>` effectively.

## Core concept

Think of **collections** as containers that hold groups of items, much like a box filled with toys or a library shelf holding books. Each type of collection is designed for specific use cases:

- **`List<T>`**: A dynamic array-like structure where elements are stored in order and can be accessed by index.
- **`Dictionary<TKey,TValue>`**: A key-value store that allows fast lookups based on unique keys, similar to a dictionary book where words (keys) map to definitions (values).
- **`IEnumerable<T>`**: An interface for iterating over collections without caring about their internal structure, like walking through items in a shopping cart.

## How it works

### `List<T>`

- **Storage**: Elements are stored contiguously on the heap.
- **Access**: Fast random access by index (`O(1)`).
- **Insertion/Deletion**: Efficient at the end (`O(1)`) but slower elsewhere due to potential resizing and shifting.

### `Dictionary<TKey,TValue>`

- **Storage**: Uses a hash table internally, where keys are hashed to determine storage locations.
- **Access**: Fast lookups by key (`O(1)` on average).
- **Insertion/Deletion**: Efficient as long as the hash function distributes keys well.

### `IEnumerable<T>`

- **Purpose**: Provides a way to iterate over collections without exposing their internal implementation details.
- **Implementation**: Can be lazy (e.g., LINQ queries) or eager (e.g., arrays).

## Practical examples

### Example: Using `List<T>` for Dynamic Arrays

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        // Create a list of integers
        List<int> numbers = new List<int>();

        // Add elements
        numbers.Add(10);
        numbers.Add(20);

        // Access by index
        Console.WriteLine(numbers[0]); // Outputs: 10

        // Insert at specific position
        numbers.Insert(1, 15); // [10, 15, 20]

        // Remove an element
        numbers.Remove(20);
    }
}
```

**Explanation**: `List<T>` is ideal for scenarios where you need a dynamic array that can grow or shrink. It provides methods like `Add`, `Insert`, and `Remove` to manage elements efficiently.

### Example: Using `Dictionary<TKey,TValue>` for Fast Lookups

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        // Create a dictionary mapping string keys to integer values
        Dictionary<string, int> inventory = new Dictionary<string, int>();

        // Add items
        inventory["Apples"] = 50;
        inventory["Bananas"] = 30;

        // Access by key
        Console.WriteLine(inventory["Apples"]); // Outputs: 50

        // Update value
        inventory["Apples"] += 10; // Now 60 apples

        // Check if a key exists
        if (inventory.ContainsKey("Oranges"))
        {
            Console.WriteLine("We have oranges!");
        }
    }
}
```

**Explanation**: `Dictionary<TKey,TValue>` is perfect for scenarios where you need to associate keys with values and perform fast lookups. It's especially useful when the data size is large, as it avoids linear searches.

### Example: Using `IEnumerable<T>` for Iteration

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        // Create a list of strings
        List<string> fruits = new List<string> { "Apple", "Banana", "Cherry" };

        // Use IEnumerable to iterate over the collection
        foreach (var fruit in fruits)
        {
            Console.WriteLine(fruit);
        }

        // LINQ query using IEnumerable<T>
        var filteredFruits = fruits.Where(f => f.StartsWith("A"));
        foreach (var fruit in filteredFruits)
        {
            Console.WriteLine($"Filtered: {fruit}");
        }
    }
}
```

**Explanation**: `IEnumerable<T>` allows you to iterate over collections without worrying about their underlying implementation. It's particularly useful when working with LINQ, as it enables lazy evaluation and deferred execution.

## Common mistakes & pitfalls

1. **Using the wrong collection type**:
   - Developers sometimes use a `List<T>` for lookups instead of a `Dictionary<TKey,TValue>`, leading to slower performance (`O(n)` vs `O(1)`).
2. **Ignoring capacity management in `List<T>`**:
   - Creating a large list without setting its initial capacity can lead to frequent resizing, which is costly.
3. **Assuming all collections are thread-safe**:
   - Collections like `List<T>` and `Dictionary<TKey,TValue>` are not thread-safe by default. Using them in multi-threaded scenarios without proper synchronization can cause race conditions or data corruption.
4. **Overusing LINQ for simple operations**:
   - While LINQ is powerful, using it for trivial tasks (e.g., iterating over a small list) might introduce unnecessary overhead compared to traditional loops.

## Best practices & recommendations

- **Choose the right collection type based on access patterns**:
  - Use `List<T>` when you need ordered collections with frequent insertions/deletions.
  - Use `Dictionary<TKey,TValue>` for fast lookups by key.
  - Use `IEnumerable<T>` when you only need to iterate over elements.
- **Prefer immutable collections when possible**:
  - For scenarios where data doesn't change, consider using immutable collections like `ImmutableList<T>` or `ImmutableDictionary<TKey,TValue>`. They are thread-safe and can improve performance in certain cases.
- **Be mindful of capacity management**:
  - When creating large lists, set the initial capacity to avoid frequent resizing. Use `.Capacity` property when appropriate.
- **Use LINQ judiciously**:
  - While LINQ is expressive, it's not always the most performant solution for simple operations. Profile your code and optimize where necessary.

## When NOT to use this

- **Using `List<T>` for lookups**: If you frequently need to find elements by a specific property (e.g., ID), consider using a `Dictionary<TKey,TValue>` instead.
- **Ignoring thread safety**: In multi-threaded environments, avoid sharing mutable collections without proper synchronization. Use concurrent collections like `ConcurrentBag`, `ConcurrentDictionary`, or lock-based mechanisms when necessary.

## Summary

- **`List<T>`** is ideal for ordered collections with frequent insertions and deletions but can be inefficient for lookups.
- **`Dictionary<TKey,TValue>`** provides fast key-based access (`O(1)` on average) and is perfect for scenarios requiring quick searches or associations.
- **`IEnumerable<T>`** offers flexibility in iteration without exposing implementation details, making it a cornerstone of LINQ operations.
- Performance matters when choosing collections. Always consider the trade-offs between memory usage, lookup speed, and thread safety based on your specific use case.
