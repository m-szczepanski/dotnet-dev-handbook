# 09: Exceptions and Error Handling

## What problem does this solve?

In software development, errors are inevitable—whether they arise from invalid user input, network issues, or unexpected behavior in third-party libraries. Without proper error handling, these problems can lead to crashes, data corruption, or poor user experiences. Exceptions provide a structured way to handle such issues gracefully, allowing developers to anticipate and manage failures without halting the entire application.

## Core concept

Think of exceptions as "traffic signals" for your code. Just like traffic lights guide drivers on when to stop, go, or slow down, exceptions signal that something unexpected has occurred in your program. Instead of letting the application crash abruptly, you can catch these signals and decide how to respond—whether by retrying an operation, logging the issue, or informing the user.

## How it works

In .NET, exceptions are objects derived from the `System.Exception` class (or its subclasses like `ArgumentException`, `FileNotFoundException`, etc.). When an error occurs, the runtime throws an exception, which can be caught using a `try-catch` block. If no handler is found, the application terminates with an unhandled exception.

### Key Mechanics

1. **Throwing Exceptions**: Developers or the runtime raise exceptions when something goes wrong.
2. **Catching Exceptions**: Using `try`, `catch`, and optionally `finally` blocks to handle errors gracefully.
3. **Propagation**: If no handler is found in the current scope, the exception propagates up the call stack until it's caught.

## Practical examples

### Example: Handling File I/O Errors

```csharp
using System;
using System.IO;

class Program
{
    static void Main()
    {
        string filePath = "nonexistent.txt";

        try
        {
            // Attempt to read from a file that may not exist.
            using (StreamReader reader = new StreamReader(filePath))
            {
                Console.WriteLine(reader.ReadToEnd());
            }
        }
        catch (FileNotFoundException ex)
        {
            // Handle the specific case of a missing file.
            Console.WriteLine($"Error: {ex.Message}");
            Console.WriteLine("Please check the file path and try again.");
        }
        catch (Exception ex)
        {
            // Catch any other unexpected errors.
            Console.WriteLine($"An unexpected error occurred: {ex.Message}");
        }
        finally
        {
            // Clean up resources regardless of whether an exception was thrown.
            Console.WriteLine("Execution completed.");
        }
    }
}
```

**Explanation**:

- **Why this implementation?** This example demonstrates how to handle specific exceptions (`FileNotFoundException`) separately from general errors. By catching `FileNotFoundException` first, we provide a more user-friendly message for missing files while still having a fallback for other unexpected issues.
- **Resource Management**: The `finally` block ensures that cleanup operations (like closing streams) occur regardless of whether an exception was thrown.

### Example: Custom Exception Handling

```csharp
using System;

// Define a custom exception type.
public class InsufficientFundsException : Exception
{
    public InsufficientFundsException(string message)
        : base(message)
    {
    }
}

class BankAccount
{
    private decimal balance;

    public void Withdraw(decimal amount)
    {
        if (amount > balance)
        {
            throw new InsufficientFundsException($"Insufficient funds: Current balance is {balance}.");
        }

        balance -= amount;
        Console.WriteLine($"Withdrew {amount}. New balance: {balance}");
    }
}

class Program
{
    static void Main()
    {
        BankAccount account = new BankAccount();
        try
        {
            account.Withdraw(100);
        }
        catch (InsufficientFundsException ex)
        {
            Console.WriteLine($"Error: {ex.Message}");
        }
    }
}
```

**Explanation**:  

- **Why custom exceptions?** Custom exceptions like `InsufficientFundsException` provide more context-specific information, making it easier to handle and debug issues related to specific business logic (e.g., banking operations).
- **Encapsulation of Logic**: By encapsulating the validation logic within the `Withdraw` method, we ensure that invalid states are caught early and communicated clearly.

## Common mistakes & pitfalls

1. **Overusing Exceptions for Control Flow**  
   Using exceptions to control program flow is inefficient because throwing an exception involves significant overhead (stack unwinding, memory allocation). Instead of using exceptions for normal business logic, use conditional statements (`if-else`) where possible.
2. **Ignoring Exceptions**  
   Catching exceptions without handling them properly can lead to silent failures. For example:

   ```csharp
   try
   {
       // Some risky operation.
   }
   catch (Exception)
   {
       // Do nothing!
   }
   ```

   This pattern suppresses errors, making it difficult to diagnose issues later.

3. **Swallowing Exceptions**  
   Catching exceptions and logging them without rethrowing can mask critical problems. For example:

   ```csharp
   try
   {
       // Some risky operation.
   }
   catch (Exception ex)
   {
       Console.WriteLine($"Error: {ex.Message}");
   }
   ```

   While this logs the error, it doesn't allow higher-level code to handle or escalate the issue.

## Best practices & recommendations

- **Be Specific**: Catch specific exception types rather than using a generic `catch (Exception)` block. This helps in providing more targeted handling and avoids masking unrelated issues.
  
  ```csharp
  try
  {
      // Risky operation.
  }
  catch (FileNotFoundException ex)
  {
      // Handle file not found specifically.
  }
  catch (IOException ex)
  {
      // Handle I/O errors separately.
  }
  ```

- **Use `finally` for Cleanup**: Always use the `finally` block to release unmanaged resources or perform cleanup operations, regardless of whether an exception occurred.
- **Custom Exceptions for Business Logic**: Define custom exceptions for domain-specific issues (e.g., `InsufficientFundsException`) to provide more context and make error handling easier.
- **Avoid Exception Swallowing**: Never catch exceptions without logging them or rethrowing if appropriate. This ensures that critical errors are not silently ignored.

## When NOT to use this

1. **For Normal Control Flow**  
   Exceptions should not be used for normal program flow. For example, instead of throwing an exception when a user enters invalid input, validate the input using conditional logic and provide clear feedback.
2. **In Performance-Critical Paths**  
   Throwing exceptions in performance-sensitive code can introduce significant overhead due to stack unwinding and memory allocation. Optimize such paths by avoiding unnecessary exception handling or using alternative approaches like validation before execution.
3. **For Expected Conditions**  
   If a condition is expected (e.g., checking if a file exists), use conditional logic instead of relying on exceptions. For example:

   ```csharp
   // Instead of:
   try
   {
       File.Open("file.txt", FileMode.Open);
   }
   catch (FileNotFoundException)
   {
       Console.WriteLine("File not found.");
   }

   // Use:
   if (!File.Exists("file.txt"))
   {
       Console.WriteLine("File not found.");
   }
   ```

## Summary

- **Exceptions are for handling unexpected errors**, not normal control flow.
- **Custom exceptions** provide domain-specific context, making error handling more precise.
- **Avoid anti-patterns**: Don't overuse exceptions, ignore them, or swallow critical information.
- **Use `try-catch-finally` wisely**: Catch specific exceptions, log appropriately, and ensure resources are cleaned up.
