# 05: Null, Nullable Types, and Null Safety

## What problem does this solve?

Null-related bugs are one of the most common sources of runtime errors in software development. Whether it's trying to access a property on a null object or passing an unexpected `null` value into a method, these issues can lead to crashes or unpredictable behavior. This subchapter explores how C# handles `null`, introduces nullable reference types (a modern feature), and explains the tools available to make your code more robust against null-related errors.

## Core concept

Think of `null` as an "empty" placeholder that represents the absence of a value. It's like having a box labeled "nothing inside." If you try to open the box expecting something useful, you'll encounter problems. C# provides mechanisms (like nullable reference types) to help developers explicitly manage and reason about whether a variable can be `null`, reducing the likelihood of runtime errors.

## How it works

- **`null`**: A special value that represents "no object." It's legal in both value types (`int?`) and reference types (`string`). However, by default, C# treats all reference types as potentially nullable unless explicitly marked otherwise.
- **Nullable value types** (`int?`, `bool?`): These are struct wrappers around primitive types that can hold either a valid value or `null`. They're useful when you need to represent the absence of a number or boolean.
- **Nullable reference types**: Introduced in C# 8.0, these allow developers to explicitly mark whether a reference type variable can be `null` (`string?`) or must not be `null` (`string`). The compiler enforces stricter checks based on this information.

## Practical examples

### Example: Nullable Value Type

```csharp
int? age = null; // Nullable int (can hold null)
age = 25;

if (age.HasValue) {
    Console.WriteLine($"Age is {age.Value}");
} else {
    Console.WriteLine("No age provided");
}
```

**Explanation**: `int?` allows the variable to hold either an integer value or `null`. The `.HasValue` and `.Value` properties help safely interact with nullable values.

### Example: Nullable Reference Type

```csharp
#nullable enable // Enable nullable reference types

string name = "Alice"; // Non-nullable by default (must not be null)
string? nickname = null; // Explicitly marked as nullable

// Compiler error if you try to use a potentially null value without checking:
Console.WriteLine(name.Length); // Safe, since `name` is non-null
Console.WriteLine(nickname.Length); // Error: Cannot access 'Length' on a possibly null reference.
```

**Explanation**: With nullable reference types enabled (`#nullable enable`), the compiler enforces stricter checks. You must explicitly handle cases where a variable might be `null`.

### Example: Using `?`, `!`, and Compiler Warnings

```csharp
string? email = "user@example.com";

// Safe null check using ?. (Null-conditional operator)
int length = email?.Length ?? 0;

// Explicitly asserting non-null with ! (NotNull assertion operator)
email!.ToLower(); // Only safe if you're certain `email` is not null

// Compiler warning for potential null reference
string trimmedEmail = email.Trim(); // Warning: May be null.
```

**Explanation**: The `?` and `!` operators help manage nullable types. The compiler warns when you might be using a potentially `null` value without proper checks.

## Common mistakes & pitfalls

1. **Ignoring potential null values**:
   - Failing to check for `null` before accessing properties or methods on reference types can lead to runtime exceptions (`NullReferenceException`).

2. **Misusing nullable types**:
   - Using `?` and `!` operators without understanding their implications can introduce bugs. For example, using `!` when a value might actually be `null` will crash the program.

3. **Not enabling or leveraging nullable reference types**:
   - Without `#nullable enable`, C# treats all reference types as potentially nullable but doesn't enforce strict checks. This can lead to unnoticed null-related bugs.

## Best practices & recommendations

- **Enable nullable reference types (`#nullable enable`)**: Make it explicit whether a variable can be `null`. This helps the compiler catch potential issues at compile time.
  
- **Use `?` and `!` operators judiciously**:
  - Use `?.` (null-conditional operator) to safely access properties or methods on potentially null objects.
  - Use `!` (not-null assertion operator) only when you're absolutely certain a variable is not `null`.

- **Default to non-nullable types**: Mark variables as nullable (`string?`) only when they genuinely need to represent the absence of a value. Otherwise, keep them non-nullable by default.

## When NOT to use this

- **Disabling nullable reference types entirely**:
  - While you can choose not to enable nullable reference types, doing so means missing out on compiler-enforced null checks and potential bugs that could be caught at compile time.
  
- **Overusing `!` (not-null assertion)**: Using the `!` operator excessively without proper validation can lead to runtime errors if assumptions about non-null values are incorrect.

## Summary

- `null` represents the absence of a value, which can cause runtime issues if not handled properly.
- Nullable value types (`int?`, `bool?`) allow you to represent missing or optional data for primitive types.
- Nullable reference types (enabled with `#nullable enable`) let you explicitly mark whether a reference type variable can be `null` and enforce stricter compile-time checks.
- Tools like the null-conditional operator (`?.`) and not-null assertion operator (`!`) help manage nullable values safely, but they should be used carefully to avoid introducing bugs.
