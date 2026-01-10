# 10: Classes, Structs, and Records

## What problem does this solve?

Developers often struggle with choosing between `class`, `struct`, or `record` when modeling objects in .NET. Each has distinct characteristics that affect memory management, performance, immutability, and behavior during assignments. Understanding these differences is crucial for writing efficient, maintainable code while avoiding common pitfalls.

## Core concept

Think of **classes** as the traditional way to model objects—flexible but reference-based (like a house address). **Structs** are value types that store their data directly (like cash in your wallet), making them more lightweight and suitable for small, immutable data. **Records**, introduced in .NET 5, combine the best of both worlds: they provide immutability by default with structural equality, while offering reference-like behavior when needed.

## How it works

- **Classes**: Reference types stored on the heap. They are ideal for large or complex objects that need to be shared between different parts of your application.
  
  - **Memory**: Stored as a reference (address) on the stack, pointing to data on the heap.
  - **Assignment**: Assigning one class variable to another copies only the reference—both variables point to the same object.

- **Structs**: Value types stored directly on the stack. They are lightweight and efficient for small, immutable data.

  - **Memory**: Stored inline with their actual data (on the stack or embedded in other objects).
  - **Assignment**: Assigning one struct variable to another creates a copy of its contents—changes to one do not affect the other.

- **Records**: A modern construct that combines immutability and structural equality. They are reference types by default but can be configured as value semantics using `readonly` or `with`.

  - **Memory**: Stored on the heap (reference type) unless explicitly marked with `[StructLayout(LayoutKind.Auto)]`.
  - **Assignment**: By default, records behave like classes (reference-based), but they provide structural equality and immutability features.

  > Note:
  In modern C#, the standard way to define a record on the stack (as a value type) is to use the `record struct` keyword (introduced in C# 10), rather than using attributes to modify a `record class`.

### Practical examples

#### Example: Class Behavior

```csharp
class PersonClass
{
    public string Name { get; set; }
}

PersonClass person1 = new PersonClass { Name = "Alice" };
PersonClass person2 = person1;
person2.Name = "Bob";

Console.WriteLine(person1.Name); // Outputs: Bob (both point to the same object)
```

**Explanation**: `person1` and `person2` are reference types. Assigning `person2 = person1` copies only the reference, so both variables refer to the same object in memory.

#### Example: Struct Behavior

```csharp
struct PersonStruct
{
    public string Name { get; set; }
}

PersonStruct person1 = new PersonStruct { Name = "Alice" };
PersonStruct person2 = person1;
person2.Name = "Bob";

Console.WriteLine(person1.Name); // Outputs: Alice (independent copy)
```

**Explanation**: `person1` and `person2` are value types. Assigning `person2 = person1` creates a complete copy of the struct, so changes to one do not affect the other.

#### Example: Record Behavior

```csharp
record PersonRecord(string Name);

PersonRecord person1 = new("Alice");
PersonRecord person2 = person1 with { Name = "Bob" };

Console.WriteLine(person1.Name); // Outputs: Alice (immutable by default)
Console.WriteLine(person2.Name); // Outputs: Bob (new instance created)
```

**Explanation**: `person1` and `person2` are records. Records provide structural equality and immutability by default, allowing you to create new instances with the `with` operator without affecting the original.

## Common mistakes & pitfalls

1. **Using structs for large or mutable data**:
   - Structs are value types stored on the stack (or embedded in other objects). Using them for large data can lead to inefficiencies due to copying overhead. Additionally, mutable struct fields can cause unintended side effects when passed by reference.

2. **Assuming records behave like classes**:
   - Records provide structural equality and immutability by default, which is different from traditional class behavior. Developers might expect records to be fully mutable or not enforce immutability unless explicitly configured.

3. **Ignoring performance implications of value vs reference types**:
   - Value types (structs) are more efficient for small data but can lead to boxing/unboxing overhead if used improperly. Reference types (classes and records by default) avoid this issue but require heap allocation, which might be unnecessary for simple objects.

## Best practices & recommendations

- **Use classes when you need reference semantics**:
  - For large or complex objects.
  - When sharing state between different parts of your application is required.
  
- **Use structs when you have small, immutable data**:
  - For lightweight objects that don’t require heap allocation.
  - When performance and immutability are critical.

- **Use records for modern object modeling**:
  - When you need structural equality or immutability by default.
  - To simplify code with the `with` operator for creating new instances based on existing ones.

### Specific Guidelines

1. **Classes**:
   - Use when you need reference semantics (shared state).
   - Suitable for large objects that require heap allocation.

2. **Structs**:
   - Use for small, immutable data.
   - Avoid mutable fields to prevent unintended side effects during copying.

3. **Records**:
   - Prefer records over classes when immutability and structural equality are desired.
   - Use the `with` operator to create new instances without modifying existing ones.
   - Configure as value types (`readonly`) or reference types based on your needs.

## When NOT to use this

- **Using structs for large data**:
  - Structs can lead to inefficiencies when used for large objects, as they are copied entirely during assignments. For such cases, classes are more appropriate.

- **Ignoring immutability in records**:
  - Records provide immutability by default, but developers might inadvertently make them mutable (e.g., using `mutable` fields). This can lead to unexpected behavior and violate the intended semantics.

- **Overusing reference types for small data**:
  - For simple objects like coordinates or small aggregates, classes introduce unnecessary heap allocation. Structs are more efficient in such cases.

## Summary

- **Classes**: Reference types stored on the heap, suitable for large or shared state.
- **Structs**: Value types stored directly (on stack), ideal for small, immutable data with performance benefits.
- **Records**: Modern construct combining immutability and structural equality, offering both value-like behavior (`readonly`) and reference-like behavior by default.

- Assignments behave differently:
  - Classes copy only the reference (address).
  - Structs create a complete copy of their contents.
  - Records provide structural equality and immutability by default but can be configured as either value or reference types.

- Choose based on your use case: Use classes for shared state, structs for small immutable data, and records when you need modern object modeling with immutability and structural equality.
