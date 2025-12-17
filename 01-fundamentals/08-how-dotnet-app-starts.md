# 08. How a .NET Application Starts

## 1. What problem does this solve?

When you write and run a .NET application, understanding how it starts up can help you debug issues more effectively and prepare for more advanced topics like ASP.NET Core hosting.

## 2. Core concept (simple explanation)

A .NET application begins execution at an entry point, typically the `Main` method. The runtime then loads your code into memory and executes it according to a specific hosting model.

## 3. How it works (under the hood)

- **Entry Point**: Every .NET application has a starting point called the "entry point." For console apps or libraries, this is usually the `Main` method.
- **Hosting Model**: The runtime uses a hosting model to manage how your app runs. In simple terms, it sets up the environment and ensures everything works smoothly.
- **Why Main sometimes “disappears”**: In ASP.NET Core applications, the entry point might not be explicitly visible because the framework handles much of the startup logic behind the scenes.

## 4. Practical examples

Here's a basic console app showing an entry point:

```csharp
// Program.cs
using System;

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Hello, World!");
    }
}
```

Explanation:  

- The `Main` method is the entry point. When you run this program, it prints "Hello, World!" to the console.
- In more complex apps (like ASP.NET Core), the entry point might look different because the framework handles startup logic.

## 5. Common mistakes & pitfalls

- Forgetting that every app needs an entry point can lead to runtime errors.
- Assuming `Main` is always visible in all .NET projects, especially when using frameworks like ASP.NET Core.

## 6. Best practices & recommendations

- Always ensure your project has a clear entry point.
- Use the hosting model provided by the framework for web apps (e.g., ASP.NET Core) to avoid reinventing the wheel.

## 7. When NOT to use this

You don’t need to manually manage the hosting model unless you’re building something highly custom or low-level. Let frameworks handle it when possible.

## 8. Summary

- Every .NET app starts at an entry point, usually `Main`.
- The runtime uses a hosting model to manage execution.
- In ASP.NET Core, the framework often handles startup logic, making `Main` less visible but still present under the hood.
