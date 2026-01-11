# 12: Interfaces vs Abstract Classes

## What problem does this solve?

Interfaces and abstract classes are fundamental tools in object-oriented programming that help developers manage complexity, promote code reuse, and enforce design patterns. They address the challenge of defining behavior or structure without implementing it directly, allowing for flexibility and extensibility.

## Core concept

Think of interfaces as a contract: they define what functionality must be implemented but leave the "how" to the implementer. Abstract classes, on the other hand, are like partially completed blueprints—they provide some implementation details while leaving others abstract (to be overridden). Both help in designing modular and maintainable systems.

## How it works

- **Interfaces**: Define a set of methods or properties without providing an implementation. All implementing types must fully implement these members.
- **Abstract Classes**: Can include both implemented and unimplemented members. They can also contain fields, constructors, and static members, which interfaces cannot.

In .NET, default interface methods (introduced in C# 8) allow interfaces to provide implementations for their members, blurring the line between interfaces and abstract classes slightly but maintaining key differences.

## Practical examples

Here's an example comparing interfaces and abstract classes:

### Using Interfaces

```csharp
// Define an interface
public interface IAnimal
{
    void MakeSound();
}

// Implementing class
public class Dog : IAnimal
{
    public void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}
```

**Explanation**: The `IAnimal` interface defines a contract that any animal must implement the `MakeSound` method. This is ideal when you want to enforce behavior without providing an implementation.

### Using Abstract Classes

```csharp
// Define an abstract class
public abstract class Animal
{
    public virtual void Eat()
    {
        Console.WriteLine("Eating...");
    }

    public abstract void MakeSound();
}

// Implementing class
public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}
```

**Explanation**: The `Animal` abstract class provides a default implementation for the `Eat` method but requires subclasses to implement `MakeSound`. This is useful when you want to share common behavior across implementations.

## Common mistakes & pitfalls

1. **Overusing interfaces**: Creating too many small, single-method interfaces can lead to "interface bloat," making code harder to maintain.
2. **Misusing abstract classes**: Using an abstract class when a simple interface would suffice can introduce unnecessary complexity.
3. **Ignoring default interface methods**: In C# 8+, developers might forget that interfaces can now have implementations, leading to confusion about which tool to use.

## Best practices & recommendations

- **Use interfaces**:
  - When you need to enforce behavior without providing an implementation.
  - For defining contracts between unrelated classes or when multiple inheritance is needed (since C# doesn't support multiple class inheritance).
- **Use abstract classes**:
  - When you want to provide shared functionality across implementations.
  - When you have a clear base class hierarchy where some methods can be implemented once and reused.

## When NOT to use this

- **Interfaces**: Avoid creating interfaces with just one method unless it's part of a larger design pattern (e.g., `Func<T>` or `Action`).
- **Abstract classes**: Don't use them if you don't need to share any implementation details. In such cases, an interface is more appropriate.

## Summary

- Interfaces define contracts without implementations, while abstract classes provide partial implementations.
- Default interface methods in C# 8+ allow interfaces to have implementations but maintain their core purpose as behavioral contracts.
- Choose interfaces when you want strict behavior enforcement or multiple inheritance; use abstract classes for shared functionality and base class hierarchies.
- Be mindful of overusing either tool, as it can lead to unnecessary complexity or interface bloat.
