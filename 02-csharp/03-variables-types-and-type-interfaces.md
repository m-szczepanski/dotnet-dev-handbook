# 03: Variables, Types, and Type Inference

Below are the suggested subchapter names. Use them if they fit, but feel free to come up with your own names if you think they are more appropriate.

## What problem does this solve?

Variables allow developers to store and manipulate data in their applications. Understanding how variables work, along with types and type inference, helps ensure that code is both reliable and maintainable. Without proper handling of these concepts, developers may encounter runtime errors or unexpected behavior due to incorrect assumptions about the data being used.

## Core concept

In .NET, every variable has a **type**, which defines what kind of data it can hold (e.g., numbers, strings, objects). Types ensure that operations on variables are meaningful and safe. Type inference is a feature that allows the compiler to automatically determine the type of a variable based on its initial value, reducing boilerplate code.

## How it works

### Variables

- A **variable** is a named storage location in memory where data can be stored.
- Each variable must have a specific **type**, which dictates what kind of values it can hold and what operations are valid on it.

### Types

- **Compile-time types**: These are known at compile time, ensuring type safety. For example:

  ```csharp
  int age = 25; // The compiler knows 'age' is an integer.
  ```

- **Runtime types**: Some types (e.g., `dynamic`) can be determined only during runtime execution.

### Strong typing vs dynamic typing

- **Strong typing** ensures that variables adhere strictly to their declared type. For example:

  ```csharp
  int x = "hello"; // Error: Cannot assign a string to an integer.
  ```

- **Dynamic typing**, on the other hand, allows variables to change types at runtime (e.g., using `dynamic` in C#):

  ```csharp
  dynamic y = 10; // Initially an integer.
  y = "hello";     // Now a string. No compile-time error.
  ```

### Type inference

- **Type inference** allows the compiler to deduce the type of a variable based on its initialization:

  ```csharp
  var name = "Alice"; // The compiler infers 'name' is a string (string).
  ```

- This reduces verbosity and makes code cleaner, especially for complex types.

## Practical examples

### Example: Using `var` with type inference

```csharp
// Without type inference:
string greeting = "Hello";

// With type inference using 'var':
var greeting = "Hello"; // The compiler infers the type as string.
Console.WriteLine(greeting); // Output: Hello
```

### Example: Compile-time vs runtime types

```csharp
// Compile-time type (known at compile time):
int number = 42;

// Runtime type using 'dynamic':
dynamic dynamicNumber = 42;
dynamicNumber = "Forty-two"; // Valid because 'dynamic' can change types.
```

### Example: Strong typing vs dynamic typing

```csharp
// Strongly typed:
string message = "Hello";
message = 10; // Compile-time error: Cannot assign an integer to a string.

// Dynamically typed using 'dynamic':
dynamic message = "Hello";
message = 10; // No compile-time error, but may cause runtime issues.
```

## Common mistakes & pitfalls

- **Misusing `var`**: While type inference is convenient, overusing it can make code harder to read if the inferred type isn't immediately clear:

  ```csharp
  var result = SomeComplexMethod(); // What's 'result'? (Bad practice.)
  ```
  
- **Relying too much on dynamic typing**: Using `dynamic` can lead to runtime errors that would otherwise be caught at compile time. It should be used sparingly.

## Best practices & recommendations

- Use `var` when the type is obvious from the initialization:

  ```csharp
  var count = 10; // Clear and concise.
  ```
  
- Avoid using `dynamic` unless absolutely necessary (e.g., working with COM interop or late-bound APIs).

## When NOT to use this

- **Avoid `var` when the type is unclear**: If the inferred type isn't immediately obvious, explicitly declare it for clarity:

  ```csharp
  // Bad: What's 'result'?
  var result = SomeComplexMethod();

  // Better: Explicitly declare the type.
  Dictionary<string, int> result = SomeComplexMethod();
  ```

- **Avoid dynamic typing in performance-critical code**: Dynamic operations are slower than static ones due to runtime type checking.

## Summary

- Variables store data and must have a specific type.
- Compile-time types ensure safety at compile time, while runtime types can change during execution.
- Strong typing enforces strict adherence to declared types, whereas dynamic typing allows flexibility but may introduce runtime errors.
- Type inference (using `var`) simplifies code by letting the compiler deduce types automatically.

 ---

### Key Points

1. Variables are named storage locations with specific types.
2. Compile-time types ensure safety and predictability.
3. Strong typing enforces strict type checking, while dynamic typing allows flexibility but can lead to runtime errors.
4. Type inference (using `var`) reduces boilerplate code when the type is clear.
