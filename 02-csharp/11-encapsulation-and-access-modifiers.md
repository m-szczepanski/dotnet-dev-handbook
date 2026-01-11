# 11: Encapsulation & Access Modifiers  

## What problem does this solve?

Encapsulation addresses a common developer frustration: managing complex systems where data is shared across multiple components. Without encapsulation, developers often struggle with maintaining data integrity and controlling how objects interact. For example, exposing internal state directly can lead to bugs when other parts of the code modify it unexpectedly. Encapsulation simplifies this by providing controlled access, reducing errors, and making maintenance easier.

## Core concept

Imagine you're building a house. You don't want everyone in the neighborhood to walk into your living room and rearrange your furniture or change the wiring without asking. Instead, you provide specific entry points (like doors) and control who can enter and what they can do inside. Similarly, encapsulation allows you to hide the internal details of an object while exposing only what is necessary for other parts of the system to interact with it.

## How it works

Encapsulation uses access modifiers (`public`, `private`, `internal`) to define how different parts of your code can interact with a class. By default, fields and methods are private unless explicitly marked otherwise. This ensures that only controlled access points (like getters/setters or public methods) can modify the internal state of an object.

At runtime, encapsulation helps prevent unintended modifications by enforcing rules around data access. For example, if you try to access a `private` field from outside its class, the compiler will throw an error, ensuring that only valid interactions occur.

## Practical examples

Here's a simple C# example demonstrating encapsulation:

```csharp
public class BankAccount {
    private decimal balance; // Private field

    public void Deposit(decimal amount) {
        if (amount > 0) {
            balance += amount;
        }
    }

    public void Withdraw(decimal amount) {
        if (balance >= amount && amount > 0) {
            balance -= amount;
        } else {
            Console.WriteLine("Insufficient funds or invalid withdrawal.");
        }
    }

    public decimal GetBalance() {
        return balance;
    }
}

// Usage
var account = new BankAccount();
account.Deposit(100);
Console.WriteLine($"Current Balance: {account.GetBalance()}"); // Output: 100
```

**Why this implementation was chosen:**

- The `balance` field is private to prevent direct manipulation from outside the class.
- Public methods (`Deposit`, `Withdraw`) provide controlled access, ensuring that only valid operations are performed (e.g., no negative deposits or withdrawals).
- This design ensures data integrity and makes it harder for external code to introduce bugs.

## Common mistakes & pitfalls

1. **Exposing internal state directly**:
   - Instead of using getters/setters, developers sometimes make fields public. For example:

     ```csharp
     public class BankAccount {
         public decimal balance; // Bad: Direct exposure
     }
     ```

     This allows external code to modify `balance` without validation, leading to invalid states.
2. **Over-reliance on getters/setters**:
   - Making every field accessible via a getter or setter can lead to an "anemic domain model," where the class becomes little more than a data container with no meaningful behavior.
3. **Ignoring access modifiers**:
   - Failing to use appropriate access modifiers (e.g., making everything `public` by default) can result in code that is harder to maintain and debug, as internal details are exposed unnecessarily.

## Best practices & recommendations

- **Use private fields with controlled access**: Always make fields private unless there's a specific reason to expose them. Use public methods or properties to control how data is accessed.
- **Validate inputs**: In getters/setters or public methods, validate input parameters to ensure they are valid before proceeding.
- **Follow SOLID principles**: Encapsulation aligns with the Single Responsibility Principle (SRP) by keeping related data and behavior together in a single class.
- **Avoid over-encapsulation**: Don't make everything private if it doesn't need to be. Use encapsulation where it provides real benefits, such as protecting sensitive data or enforcing business rules.

## When NOT to use this

Encapsulation is generally beneficial, but there are cases where it might not be necessary:

- **Simple utility classes**: If a class is purely functional (e.g., a math utility with no internal state), encapsulation may add unnecessary complexity.
- **Data transfer objects (DTOs)**: In some scenarios, DTOs are designed to be simple containers for data without behavior. Making fields public in such cases can simplify serialization and deserialization.

## Summary

1. Encapsulation hides the internal details of a class while exposing only what is necessary, promoting data integrity and maintainability.
2. Access modifiers (`public`, `private`, `internal`) control how different parts of your code interact with a class.
3. Common pitfalls include over-exposing fields or relying too heavily on getters/setters without meaningful validation.
4. Best practices involve using private fields with controlled access and validating inputs in public methods.
5. Encapsulation is essential for complex systems but may not be necessary for simple utility classes or DTOs.
