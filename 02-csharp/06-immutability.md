# 06: Immutability in C#

## What problem does this solve?

Immutability is a powerful concept that helps developers write more reliable, thread-safe, and predictable code. However, understanding when to use immutable objects versus mutable ones can be challenging. This subchapter explores the differences between mutable and immutable objects, why immutability matters in certain scenarios, and how it impacts bugs and concurrency.

## Core concept

Think of an **immutable object** as a "sealed box" that cannot be changed once created. Once you've set its contents (e.g., data or state), those values remain constant forever. On the other hand, a **mutable object** is like a regular box where you can open it and modify its contents at any time.

## How it works

- **Immutable objects**: These are designed to be unchangeable after creation. Any operation that appears to "modify" an immutable object actually creates a new instance with the updated state.
- **Mutable objects**: Allow their internal state to change over time through methods or direct property access. This flexibility can lead to unintended side effects, especially in concurrent environments.

## Practical examples

### Example: Mutable vs Immutable Object

```csharp
// Mutable class example
public class MutablePoint {
    public int X { get; set; }
    public int Y { get; set; }

    public void Move(int newX, int newY) {
        X = newX;
        Y = newY;
    }
}

// Immutable class example
public readonly struct ImmutablePoint {
    public readonly int X { get; }
    public readonly int Y { get; }

    public ImmutablePoint(int x, int y) {
        X = x;
        Y = y;
    }

    // Create a new instance with updated coordinates
    public ImmutablePoint WithNewCoordinates(int newX, int newY) =>
        new ImmutablePoint(newX, newY);
}

// Usage comparison
var mutablePoint = new MutablePoint { X = 10, Y = 20 };
mutablePoint.Move(30, 40); // Modifies the existing object

var immutablePoint = new ImmutablePoint(10, 20);
immutablePoint = immutablePoint.WithNewCoordinates(30, 40); // Creates a new instance
```

**Explanation**: The `MutablePoint` class allows its state to be changed directly or through methods. In contrast, the `ImmutablePoint` struct ensures that any change results in a completely new object, preserving the original state.

## Common mistakes & pitfalls

1. **Overusing mutable objects**:
   - Mutable objects can lead to unintended side effects when shared between different parts of your code. For example, modifying one instance might affect other references pointing to the same object.

2. **Ignoring immutability in concurrent scenarios**:
   - In multi-threaded environments, mutable state can cause race conditions or inconsistent behavior if threads access and modify data simultaneously without proper synchronization.

3. **Creating unnecessary copies with immutable objects**:
   - While immutability ensures safety, it often requires creating new instances for every change. This can lead to performance overhead in scenarios where frequent updates are needed.

## Best practices & recommendations

- **Use immutability when state consistency is critical**: For example, in domain models or value types that represent concepts like coordinates, dates, or monetary amounts.
  
- **Leverage immutable collections**:
  - C# provides immutable versions of common collection types (e.g., `ImmutableList<T>`, `ImmutableDictionary<TKey, TValue>`). These are particularly useful when you need to ensure data integrity in concurrent environments.

- **Balance immutability with performance**: For scenarios where frequent updates are required and thread safety is not a concern, mutable objects might be more efficient. However, always consider the trade-offs.

## When NOT to use this

- **Forcing immutability everywhere**:
  - While immutable objects provide strong guarantees, they can introduce unnecessary complexity or performance overhead in cases where state changes are expected and safe.

- **Ignoring mutability when it's appropriate**: Mutable objects are essential for scenarios like temporary data structures, caching mechanisms, or internal algorithms where frequent updates are required without the need for thread safety.

## Summary

- Immutability ensures that once an object is created, its state cannot change. This makes immutable objects safer to use in concurrent environments and reduces unintended side effects.
- Mutable objects allow changes to their state but can introduce complexity, especially when shared across threads or used in scenarios where consistency is critical.
- Choosing between mutable and immutable depends on the context: Use immutability for data that should remain constant (e.g., domain models), and use mutability for temporary or frequently changing state.
