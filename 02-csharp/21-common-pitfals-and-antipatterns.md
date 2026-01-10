# 21: Common C# Pitfalls and Anti-Patterns

This subchapter addresses some of the most common mistakes developers make when writing C# code. By understanding these pitfalls, you can avoid anti-patterns that lead to brittle, unmaintainable, or inefficient software. The goal is to share hard-earned lessons that help you write cleaner, more effective code.

## What problem does this solve?

Developers often fall into traps like overusing inheritance, creating god classes, mismanaging asynchronous operations, or optimizing prematurely. These practices can lead to:

- **Brittle Code**: Over-reliance on inheritance or large monolithic classes makes code difficult to maintain and extend.
- **Performance Issues**: Misuse of async/await or premature optimization can introduce bugs or unnecessary complexity.
- **Unreadable Code**: Anti-patterns like god classes make it hard for other developers (or even yourself) to understand the system.

This subchapter provides clear guidance on recognizing these pitfalls and adopting better practices.

## Core concept

Think of your codebase as a house. Just as you wouldn't build a house with shaky foundations or overly complex wiring, you should avoid anti-patterns that make your code fragile or hard to maintain. By following established principles (like SOLID) and best practices, you can ensure your "house" is sturdy, scalable, and easy to live in.

## How it works

### Overusing Inheritance

- **Inheritance** allows a class to inherit properties and methods from another class. However, over-reliance on inheritance can lead to tight coupling between classes, making them difficult to modify or extend independently.
  
### God Classes

- A "god class" is a single class that handles too many responsibilities. This violates the Single Responsibility Principle (SRP) and makes code harder to test, maintain, or understand.

### Misusing Async/Await

- Asynchronous programming in C# (`async`/`await`) is powerful but can lead to bugs if misused. For example:
  - **Deadlocks**: Blocking on async methods using `.Result` or `.Wait()`.
  - **Unnecessary async**: Marking synchronous methods as `async` without any asynchronous operations.

### Premature Optimization

- Optimizing code before it's proven to be a bottleneck can lead to unnecessary complexity. It often results in harder-to-read and maintain code, with minimal performance gains.

## Practical examples

### Example: Overusing Inheritance vs Composition

```csharp
// Bad: Over-reliance on inheritance
public class Employee : Person, IEmployee // Multiple inheritance
{
    public void Work() { /* ... */ }
}

// Better: Use composition instead of inheritance
public class Employee
{
    private readonly Person _person;
    
    public Employee(Person person)
    {
        _person = person;
    }

    public string GetName() => _person.Name; // Delegate to the composed object
}
```

**Explanation**: Instead of inheriting from multiple classes, composition allows you to reuse functionality without tight coupling. This makes your code more flexible and easier to maintain.

### Example: God Class vs Separation of Concerns

```csharp
// Bad: God class handling too many responsibilities
public class OrderProcessor
{
    public void ProcessOrder() { /* ... */ }
    public void ValidatePayment() { /* ... */ }
    public void SendEmailNotification() { /* ... */ }
}

// Better: Separate concerns into smaller classes
public class OrderValidator
{
    public bool IsValid(Order order) { /* ... */ }
}

public class PaymentProcessor
{
    public void ProcessPayment(Order order) { /* ... */ }
}

public class NotificationService
{
    public void SendNotification(Order order) { /* ... */ }
}
```

**Explanation**: Breaking down responsibilities into smaller, focused classes adheres to the Single Responsibility Principle (SRP), making each component easier to test and maintain.

### Example: Misusing Async/Await

```csharp
// Bad: Blocking on async code using .Result
public void ProcessData()
{
    var result = GetDataAsync().Result; // Can cause deadlocks!
}

// Better: Use await instead of blocking
public async Task ProcessDataAsync()
{
    var result = await GetDataAsync();
}
```

**Explanation**: Using `.Result` or `.Wait()` on an `async` method can lead to deadlocks, especially in UI or ASP.NET Core applications. Always use `await` when working with asynchronous operations.

### Example: Premature Optimization

```csharp
// Bad: Premature optimization of a simple loop
public int Sum(int[] numbers)
{
    // Unnecessary micro-optimization
    int sum = 0;
    for (int i = 0; i < numbers.Length; i++)
    {
        sum += numbers[i];
    }
    return sum;
}

// Better: Keep it simple unless profiling shows a bottleneck
public int Sum(int[] numbers)
{
    return numbers.Sum(); // Built-in LINQ method is usually sufficient
}
```

**Explanation**: In this case, using `numbers.Sum()` is simpler and more readable. Premature optimization can lead to harder-to-maintain code without significant performance benefits.

## Common mistakes & pitfalls

1. **Overusing inheritance instead of composition**: This leads to tight coupling between classes, making them difficult to modify or extend.
2. **Creating god classes**: Violating SRP by putting too many responsibilities into a single class makes the code harder to maintain and test.
3. **Misusing async/await**: Blocking on asynchronous operations (`Result`, `Wait`) can cause deadlocks, while unnecessary async methods add complexity without benefits.
4. **Premature optimization**: Optimizing code before identifying actual bottlenecks often results in more complex code with minimal performance gains.

## Best practices & recommendations

- **Prefer composition over inheritance** to keep classes loosely coupled and easier to modify.
- **Adhere to SRP (Single Responsibility Principle)** by breaking large classes into smaller, focused components.
- **Use async/await correctly**: Avoid blocking on asynchronous operations (`Result`, `Wait`), and only mark methods as `async` when they contain actual asynchronous work.
- **Avoid premature optimization**: Focus on writing clean, maintainable code first. Optimize only after profiling has identified specific bottlenecks.

## When NOT to use this

1. **When inheritance is genuinely appropriate**: Inheritance can be useful in well-defined hierarchies (e.g., polymorphism for different implementations of an interface).
2. **For simple, single-responsibility classes**: Small utility classes that handle a single task don't need further decomposition.
3. **When async operations are truly necessary**: Asynchronous programming is essential for I/O-bound tasks like file access or network requests.

## Summary

- **Avoid overusing inheritance**; instead, favor composition to keep your code loosely coupled and maintainable.
- **Break down god classes** into smaller components that adhere to the Single Responsibility Principle (SRP).
- **Use async/await correctly**: Avoid blocking on asynchronous operations (`Result`, `Wait`) and only mark methods as `async` when they contain actual asynchronous work.
- **Optimize only after profiling**: Focus on writing clean, maintainable code first. Premature optimization can lead to unnecessary complexity without significant performance benefits.

By recognizing these common pitfalls and adopting better practices, you can write more robust, scalable, and readable C# applications.
