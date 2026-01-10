# 07. Control Flow Fundamentals

## What problem does this solve?

Control flow constructs like `if`, `switch`, and pattern matching help developers make decisions in their code based on conditions or patterns. Without these tools, logic would be rigid and unable to adapt to varying inputs or states, leading to brittle software that fails under unexpected scenarios.

## Core concept

Imagine you're at a restaurant deciding what to order. You evaluate your hunger level (`if`), choose from the menu options (`switch`), and sometimes make decisions based on specific characteristics of the dish (e.g., "Is it spicy?"—pattern matching). Control flow in programming is similar: it guides the execution path based on conditions or patterns, ensuring the right logic runs for each situation.

## How it works

### `if`

The `if` statement evaluates a boolean condition. If true, the code block inside executes; otherwise, it skips to the next part of the program. This allows developers to branch their logic dynamically.

### `switch`

The `switch` statement provides a way to evaluate multiple conditions in an organized manner. It matches a value against several cases and executes the corresponding block when a match is found. Modern .NET versions enhance `switch` with more flexible matching capabilities, including pattern support.

## Pattern Matching

Pattern matching allows developers to check for specific shapes or characteristics of data (e.g., types, properties) without explicitly writing complex conditional logic. It's particularly useful in scenarios where you need to handle different cases based on the structure of an object.

## Practical examples

### Example: Using `if` and `switch`

```csharp
// Example: Determining a grade based on score using if-else
int score = 85;

string grade;
if (score >= 90)
{
    grade = "A";
}
else if (score >= 80)
{
    grade = "B";
}
else if (score >= 70)
{
    grade = "C";
}
else
{
    grade = "F";
}

// Example: Using switch with pattern matching in .NET 8+
var shape = new Circle { Radius = 5 };

string description;
switch (shape)
{
    case Circle c when c.Radius > 10:
        description = $"Large circle with radius {c.Radius}";
        break;
    case Square s:
        description = $"Square with side length {s.SideLength}";
        break;
    default:
        description = "Unknown shape";
        break;
}
```

**Explanation:**

- The `if` example shows how to evaluate multiple conditions sequentially.
- The `switch` example demonstrates pattern matching, where we check both the type (`Circle`, `Square`) and additional conditions (e.g., radius size).

### Expressions vs Statements

In .NET 8+, you can use expression-based control flow with `if` and `switch`. For instance:

```csharp
// Expression-based if
var result = score >= 90 ? "A" : score >= 80 ? "B" : "C";

// Expression-based switch
string description = shape switch
{
    Circle c when c.Radius > 10 => $"Large circle with radius {c.Radius}",
    Square s => $"Square with side length {s.SideLength}",
    _ => "Unknown shape"
};
```

**Why use expressions?**

- They are more concise and can be used in places where statements aren't allowed (e.g., method return values).
- Expressions often lead to cleaner, more readable code when the logic is simple.

## Common mistakes & pitfalls

1. **Overusing `else` chains:** Long chains of `if-else` can become hard to read and maintain. Consider using `switch` or refactoring into smaller methods for better clarity.
2. **Ignoring null checks in pattern matching:** Pattern matching doesn't automatically handle null values, so developers must be explicit about null cases unless they use the new `.NET 8+` null-forgiving patterns.
3. **Confusing expressions with statements:** While expression-based control flow is powerful, it can lead to confusion if overused or misapplied in complex scenarios. Always ensure that using an expression aligns with readability and maintainability.

## Best practices & recommendations

- **Prefer `switch` over nested `if`s** when you have multiple conditions to evaluate.
- **Use pattern matching** for type-based decisions, especially when combined with additional constraints (e.g., property values).
- **Be explicit about null handling** in pattern matching unless using the new `.NET 8+` features that simplify this.
- **Choose between statements and expressions based on readability**: Use expressions for simple logic but stick to statements when clarity is more important.

## When NOT to use this

1. **Overcomplicating `switch`:** Avoid adding too many cases or complex conditions in a single `switch`. If the logic becomes unwieldy, consider refactoring into smaller methods or using strategy patterns.
2. **Using pattern matching for trivial checks:** For simple type checks (e.g., `if (obj is string)`), stick to traditional casting or type checking unless you need additional pattern-based conditions.
3. **Ignoring performance implications:** While modern .NET optimizes control flow, overly complex logic can still impact performance. Profile your code if control flow becomes a bottleneck.

## Summary

- Control flow constructs like `if`, `switch`, and pattern matching are essential for making decisions in code.
- Pattern matching simplifies handling different data shapes or characteristics.
- Expression-based control flow offers conciseness but should be used judiciously to maintain readability.
- Always consider the trade-offs between simplicity, performance, and maintainability when choosing how to implement control flow.
