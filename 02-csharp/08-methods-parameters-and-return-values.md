# 08. Methods, Parameters, and Return Values

## What problem does this solve?

Methods are the building blocks of reusable code in any programming language. They encapsulate logic, making it easier to manage complexity by breaking problems into smaller, manageable pieces. Parameters allow methods to accept input dynamically, while return values provide a way for methods to communicate results back to the caller. Without these features, developers would have to write repetitive or monolithic code, leading to maintainability issues and bugs.

## Core concept

Think of methods as reusable functions in your daily life. For example, if you're cooking, a "recipe" is like a method: it takes ingredients (parameters) and produces food (return value). Similarly, in programming, methods take inputs (parameters), perform operations, and return results to the caller.

## How it works

### Methods

A method is a block of code that performs a specific task. It can accept parameters (inputs) and optionally return a value. In .NET, methods are defined using the `public`, `private`, or other access modifiers followed by the return type and method name.

### Parameters

Parameters allow methods to receive data dynamically. There are several types:

- **`ref`:** Allows passing variables by reference so changes inside the method affect the original variable.
- **`out`:** Used for output parameters that must be assigned a value before returning from the method.
- **`in`:** Introduced in .NET 5, it allows readonly references to improve performance and safety.

### Return Values

Methods can return values using the `return` keyword. If no value is returned, the method's return type should be `void`. Expression-bodied methods provide a concise way to define simple methods with a single expression.

## Practical examples

### Example: Using `ref`, `out`, and `in`

```csharp
// Method with ref parameter (modifies original variable)
public void Swap(ref int x, ref int y)
{
    int temp = x;
    x = y;
    y = temp;
}

// Method with out parameter (must assign value before return)
public void GetCoordinates(out int x, out int y)
{
    x = 10; // Must initialize
    y = 20; // Must initialize
}

// Method with in parameter (readonly reference for performance)
public int CalculateSum(in int[] numbers)
{
    int sum = 0;
    foreach (var number in numbers)
    {
        sum += number;
    }
    return sum;
}
```

**Explanation:**

- The `Swap` method uses `ref` to modify the original variables passed by reference.
- The `GetCoordinates` method uses `out` parameters, which must be assigned before returning.
- The `CalculateSum` method uses an `in` parameter for readonly access to the array, improving performance and safety.

### Expression-bodied methods

```csharp
// Traditional method definition
public int Add(int a, int b)
{
    return a + b;
}

// Expression-bodied method (concise syntax)
public int Multiply(int a, int b) => a * b;

// Method with optional parameters
public string FormatName(string firstName = "John", string lastName = "Doe")
{
    return $"{firstName} {lastName}";
}
```

**Why use expression-bodied methods?**

- They provide a more concise syntax for simple logic.
- They are ideal when the method's implementation fits on one line.

### Optional parameters

```csharp
// Method with optional parameters
public void Greet(string name = "User", int age = 0)
{
    Console.WriteLine($"Hello, {name}! You are {age} years old.");
}

// Usage examples
Greet(); // Uses default values: Hello, User! You are 0 years old.
Greet("Alice"); // Uses default age: Hello, Alice! You are 0 years old.
```

**Explanation:**

- Optional parameters allow methods to be called with fewer arguments than defined. Default values are used when no value is provided.

## Common mistakes & pitfalls

1. **Misusing `ref` and `out`:**
   - Using `ref` when the method doesn't need to modify the original variable.
   - Forgetting to assign a value to an `out` parameter before returning, which throws a compile-time error.
2. **Overloading with optional parameters:**
   - Overloading methods with optional parameters can lead to ambiguity if default values are not carefully managed.
3. **Ignoring performance implications of `in`:**
   - While `in` improves performance by avoiding copying readonly data, it's only beneficial for large or complex types. For simple types (e.g., `int`, `string`), the overhead might outweigh the benefit.

## Best practices & recommendations

- **Use `ref` sparingly:** Only use `ref` when you explicitly need to modify the original variable passed by reference.
- **Be explicit with `out`:** Always ensure that all `out` parameters are assigned before returning from a method.
- **Leverage `in` for readonly references:** Use `in` for large or complex types (e.g., arrays, collections) to improve performance and safety without unnecessary copying.
- **Keep optional parameters minimal:** Limit the number of optional parameters in a method to avoid ambiguity and maintain readability. Consider using named arguments when calling methods with multiple optional parameters.
- **Use expression-bodied methods judiciously:** While they offer conciseness, reserve them for simple logic that fits on one line. For complex operations, stick to traditional method definitions for better readability.

## When NOT to use this

1. **Overusing `ref` and `out`:** Avoid using `ref` or `out` when the method doesn't require modifying the original variable or returning multiple values. Excessive use can make code harder to understand.
2. **Using optional parameters excessively:** While optional parameters are convenient, overloading methods with too many default values can lead to ambiguity and maintenance issues. Consider refactoring into smaller, more focused methods if needed.
3. **Ignoring performance trade-offs of `in`:** For simple types (e.g., primitive types or small objects), the overhead of using `in` might outweigh its benefits. Use it primarily for large or complex readonly data structures.

## Summary

- Methods are essential for encapsulating reusable logic, and parameters allow them to accept dynamic input.
- `ref`, `out`, and `in` provide powerful ways to manage how variables are passed into methods, but they should be used judiciously based on the use case.
- Optional parameters simplify method calls by providing default values, but overuse can lead to ambiguity.
- Expression-bodied methods offer a concise syntax for simple logic but should not replace traditional definitions when complexity increases.
