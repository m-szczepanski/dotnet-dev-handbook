# 13: Dependency Basics

## What problem does this solve?

Dependency management is a critical aspect of software architecture. Understanding how to manage dependencies without relying on advanced frameworks like dependency injection containers helps developers build more maintainable and testable code. This subchapter focuses on the basics of managing dependencies manually, highlighting why certain approaches are problematic and offering practical alternatives.

## Core concept

Dependencies are resources or services that a class relies on to perform its functions. Managing these dependencies effectively ensures that classes remain loosely coupled, making them easier to test, maintain, and extend. Without proper dependency management, code can become tightly coupled, leading to fragility and difficulty in testing.

## How it works

- **Constructor Dependencies**: Passing required resources or services through a class's constructor is the most straightforward way to manage dependencies. This makes dependencies explicit and ensures that classes are only responsible for their own behavior.
  
- **Manual Dependency Wiring**: In scenarios without DI frameworks, developers must manually wire up dependencies by creating instances of dependent objects and passing them into constructors or methods.

## Practical examples

Here's an example demonstrating constructor dependencies versus relying on `new` everywhere:

### Constructor Dependencies

```csharp
// Define a service interface
public interface IMessageService
{
    void SendMessage(string message);
}

// Implement the service
public class EmailMessageService : IMessageService
{
    public void SendMessage(string message)
    {
        Console.WriteLine($"Sending email: {message}");
    }
}

// A class that depends on IMessageService
public class NotificationManager
{
    private readonly IMessageService _messageService;

    // Constructor dependency injection
    public NotificationManager(IMessageService messageService)
    {
        _messageService = messageService;
    }

    public void NotifyUser(string message)
    {
        _messageService.SendMessage(message);
    }
}

// Usage
public class Program
{
    static void Main()
    {
        IMessageService messageService = new EmailMessageService();
        NotificationManager manager = new NotificationManager(messageService);

        manager.NotifyUser("Hello, user!");
    }
}
```

**Explanation**: The `NotificationManager` depends on an `IMessageService`, which is passed into its constructor. This makes the dependency explicit and allows for easy swapping of implementations (e.g., switching from email to SMS).

### Problem with `new` Everywhere

```csharp
// A class that directly creates dependencies using 'new'
public class NotificationManagerWithNew
{
    private readonly IMessageService _messageService;

    public NotificationManagerWithNew()
    {
        // Directly creating the dependency
        _messageService = new EmailMessageService();
    }

    public void NotifyUser(string message)
    {
        _messageService.SendMessage(message);
    }
}

// Usage
public class Program
{
    static void Main()
    {
        NotificationManagerWithNew manager = new NotificationManagerWithNew();

        manager.NotifyUser("Hello, user!");
    }
}
```

**Explanation**: In this case, `NotificationManagerWithNew` directly creates its dependency (`EmailMessageService`) using the `new` keyword. This tightly couples the class to a specific implementation and makes it harder to test or replace.

## Common mistakes & pitfalls

1. **Using 'new' everywhere**: Directly creating dependencies within classes leads to tight coupling, making code difficult to test and maintain.
2. **Hardcoding implementations**: Relying on concrete types instead of interfaces can make the system inflexible and prone to breaking changes.
3. **Ignoring dependency injection principles**: Failing to pass dependencies explicitly (e.g., via constructors) can result in hidden dependencies that are hard to track.

## Best practices & recommendations

- **Use constructor injection**:
  - Pass all required dependencies through a class's constructor. This makes dependencies explicit and ensures that classes are only responsible for their own behavior.
- **Prefer interfaces over concrete types**:
  - Define dependencies using interfaces rather than concrete implementations to promote loose coupling and flexibility.
- **Avoid 'new' in constructors or methods**:
  - Refrain from creating instances of dependent objects within a class. Instead, rely on external components (e.g., the caller) to provide those dependencies.

## When NOT to use this

- **Overusing constructor injection**: While constructor injection is generally beneficial, overloading constructors with too many parameters can make them unwieldy and difficult to manage.
- **Ignoring dependency management entirely**: Failing to manage dependencies properly can lead to tightly coupled code that is hard to test or maintain.

## Summary

- Constructor dependencies are a fundamental way to manage dependencies explicitly, promoting loose coupling and testability.
- Relying on `new` everywhere leads to tight coupling, making it difficult to replace implementations or write unit tests.
- Manual dependency wiring involves creating instances of dependent objects manually but passing them into constructors instead of hardcoding their creation within the class.
- Favor interfaces over concrete types and constructor injection over direct instantiation for better maintainability and flexibility.
