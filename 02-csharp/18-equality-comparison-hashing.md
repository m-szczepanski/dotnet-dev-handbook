# 18. Equality, Comparison, and Hashing

## What problem does this solve?

Equality and comparison are fundamental concepts in programming that help developers determine whether two objects or values are the same. However, many developers overlook the nuances between reference equality (`==`) and value equality (`.Equals`), leading to bugs or unexpected behavior. Additionally, hashing plays a crucial role in data structures like dictionaries and hash sets, where efficient lookup is essential.

This subchapter aims to clarify these concepts, ensuring you can write robust code that handles equality checks correctly and leverages hashing effectively.

## Core concept

Equality and comparison are like asking whether two things are "the same" or "different." For example:

- Are two strings the same (`"hello"` vs `"hello"`)?
- Are two objects pointing to the same memory location?

Hashing, on the other hand, is like assigning a unique identifier (hash code) to an object so that it can be quickly located in a collection. It's similar to how you might organize items by their labels or tags for easy retrieval.

## How it works

### `==` vs `.Equals`

- **Reference Equality (`==`)**: Compares whether two references point to the same memory location (i.e., are they the exact same object?).
- **Value Equality (`.Equals`)**: Compares whether two objects have the same value, regardless of their memory locations.

### Value equality

When comparing values, you often want to check if two objects represent the same logical data. For example:

- Two `int`s with the same numeric value are considered equal.
- Two strings with identical characters in the same order are considered equal.

### `GetHashCode`

The `GetHashCode` method generates a unique integer for an object based on its contents. This hash code is used by collections like dictionaries and hash sets to quickly locate items without scanning every element.

## Practical examples

Here’s how equality and hashing work in practice:

```csharp
using System;

class Program
{
    static void Main()
    {
        // Reference Equality (==)
        int a = 42;
        int b = 42;
        Console.WriteLine(a == b); // True, because they have the same value

        string str1 = "hello";
        string str2 = "hello";
        Console.WriteLine(str1 == str2); // True, reference equality for strings (interning)

        // Value Equality (.Equals)
        int[] array1 = new int[] { 1, 2, 3 };
        int[] array2 = new int[] { 1, 2, 3 };

        Console.WriteLine(array1.Equals(array2)); // False, default implementation checks reference equality
        Console.WriteLine(array1.SequenceEqual(array2)); // True, using SequenceEqual for value comparison

        // GetHashCode Example
        string hashString = "example";
        int hashCode = hashString.GetHashCode();
        Console.WriteLine($"Hash code for '{hashString}': {hashCode}");
    }
}
```

### Explanation

- `==` checks if two references point to the same memory location (or, in the case of strings, leverages interned strings).
- `.Equals` is used for value comparison but defaults to reference equality unless overridden.
- `GetHashCode` generates a hash code based on an object's contents, which is crucial for efficient lookup in collections.

## Common mistakes & pitfalls

1. **Confusing Reference and Value Equality**: Using `==` when you meant `.Equals`, or vice versa, can lead to bugs—especially with reference types.
2. **Overriding Equals Without Overriding GetHashCode**: If you override `.Equals`, you must also override `GetHashCode` to maintain consistency (objects that are equal should have the same hash code).
3. **Assuming Hash Codes Are Unique**: While hash codes aim for uniqueness, collisions can occur due to their limited range.

## Best practices & recommendations

- **Use `.Equals` for Value Equality**: Always use `.Equals` when comparing objects by value unless you specifically need reference equality.
- **Override Equals and GetHashCode Together**: If you override `.Equals`, ensure `GetHashCode` is also overridden to maintain consistency.
- **Be Careful with Reference Types**: For custom types, implement `.Equals` and `GetHashCode` properly to avoid bugs in collections like dictionaries or hash sets.

## When NOT to use this

While equality checks are essential for most applications, there may be cases where you intentionally want reference-based comparisons (e.g., when tracking specific object instances). However, for general-purpose comparison logic, value equality is usually the correct approach. Additionally, avoid overriding `GetHashCode` unnecessarily unless your type will be used in hash-based collections.

## Summary

- Reference Equality (`==`) checks if two references point to the same memory location.
- Value Equality (`.Equals`) compares whether two objects have the same logical content.
- `GetHashCode` generates a unique integer for an object, crucial for efficient lookup in hash-based collections.
- Always use `.Equals` when comparing values unless reference equality is explicitly needed.
- Override both `.Equals` and `GetHashCode` together to maintain consistency in custom types.
