# 02: Program Structure & Entry Point

This subchapter explains how C# programs are structured, where execution begins, and what happens behind the scenes during compilation.

## What problem does this solve?

Understanding program structure is essential for developers to write maintainable code and debug issues effectively. Knowing where a program starts (the entry point) helps in organizing logic and ensuring that all components work together seamlessly. Additionally, understanding how the compiler processes your code provides insights into performance and behavior.

## Core concept

In C#, every executable program has an **entry point**, which is typically the `Main` method. This method serves as the starting point for execution. Over time, C# introduced **top-level statements** to simplify small programs by eliminating the need for explicit classes and methods.

## How it works

### Main Method

- The `Main` method is the entry point of a console or Windows Forms application.
- It must be declared as `static`, allowing it to be called without creating an instance of its containing class.
- Its signature can vary, but commonly includes parameters like `string[] args` for command-line arguments.

### Top-level Statements

- Introduced in C# 9.0, top-level statements allow developers to write code directly in the file without wrapping it in a class or method.
- The compiler automatically wraps these statements into a hidden `Main` method under the hood.

#### Compilation Process

1. **Source Code**: You write your program using either traditional `Main` methods or top-level statements.
2. **Compilation**: The C# compiler (`csc.exe`) processes your code and generates Intermediate Language (IL).
3. **Execution**: The .NET runtime loads the IL and JIT-compiles it into machine-specific instructions for execution.

### What the Compiler Generates

- When you use a traditional `Main` method, the compiler directly compiles it as part of the program.
- With top-level statements, the compiler generates an implicit class and `Main` method behind the scenes. For example:

```cs
// Top-level statement code (your input)
Console.WriteLine("Hello, World!");

// What the compiler generates:
internal static class Program {
    private static void Main() {
        Console.WriteLine("Hello, World!");
    }
}
```

## Practical examples

### Traditional `Main` Method

```cs
using System;

class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Hello from Main method!");
    }
}
```

**Explanation**:

- The `Program` class contains a `static Main` method, which is the entry point.
- When you run the program, execution starts here.

### Top-level Statements

```cs
using System;

Console.WriteLine("Hello from top-level statement!");

if (args.Length > 0)
{
    Console.WriteLine($"Argument: {args[0]}");
}
```

**Explanation**:

- No explicit class or `Main` method is needed.
- The compiler automatically wraps this code into a hidden `Main` method.

## Common mistakes & pitfalls

### Mistakes Developers Make

1. **Confusing top-level statements with traditional `Main` methods**: While both work, they have different implications for larger projects.
2. **Not understanding the generated code**: When using top-level statements, developers might overlook how the compiler wraps their code into a hidden class and method.

### Consequences

- Misuse of top-level statements in large applications can lead to maintainability issues.
- Ignoring the generated `Main` method can make debugging or extending functionality more challenging.

## Best practices & recommendations

1. **Use top-level statements for small, simple programs**: They simplify code and reduce boilerplate.
2. **Stick with traditional `Main` methods for larger projects**: This provides better structure and clarity, especially when dealing with complex logic or multiple entry points.
3. **Be aware of the generated code**: When using top-level statements, keep in mind that the compiler wraps your code into a hidden class and method.

## When NOT to use this

- **Large applications**: While top-level statements are convenient for small scripts, they might not scale well for large projects with complex requirements.
- **Scenarios requiring explicit control over entry points**: If you need multiple entry points or custom logic before the main execution starts, traditional `Main` methods provide more flexibility.

## Summary

- **Main Method**:
  - The entry point of a C# program.
  - Declared as `static`, typically with a signature like `void Main(string[] args)`.
  
- **Top-level Statements**:
  - Introduced in C# 9.0 to simplify small programs.
  - Automatically wrapped into a hidden class and `Main` method by the compiler.

- **What the Compiler Generates**:
  - For traditional `Main`, it compiles directly as part of your program.
  - For top-level statements, the compiler generates an implicit class and `Main` method behind the scenes.

- **Why Main Sometimes Isn’t Visible (with Top-level Statements)**:
  - In C# 9+, when using top-level statements, the entry point is hidden because the compiler automatically wraps your code into a generated `Main` method.
  