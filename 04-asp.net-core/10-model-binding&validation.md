# 10: Model Binding & Validation

## What problem does this solve?

Model binding and validation address two critical challenges in web development:

1. **Handling User Input**: Developers often struggle with parsing raw HTTP requests (e.g., form data, query strings) into structured objects that can be easily processed.
2. **Ensuring Data Integrity**: Without proper validation, applications may accept invalid or malicious input, leading to bugs, security vulnerabilities, and incorrect behavior.

By automating these tasks, ASP.NET Core simplifies the process of working with user input while ensuring data is both usable and trustworthy.

## The Big Idea

Imagine you're running a restaurant. When customers order food, they provide details like their name, what they want to eat, and how many servings. Instead of manually writing down each detail on a notepad (raw HTTP request), your waiter uses a structured form (model binding) to capture the information neatly.

Once collected, the kitchen staff checks if the order is complete—e.g., no missing items or invalid quantities (validation). This ensures that only valid orders are processed and served.

Similarly, ASP.NET Core automates the process of mapping user input into meaningful objects (model binding) and verifying its correctness before proceeding (validation).

## Mechanics Relevant to Developers

### Model Binding

- **Automatic Mapping**: ASP.NET Core automatically maps incoming request data (form fields, query strings, JSON payloads) to action method parameters or model classes.
- **Sources of Data**:
  - Form fields (`<input>` elements).
  - Query strings (`?key=value` in URLs).
  - Route values (e.g., `{id}` in routes like `/products/{id}`).
  - Request body (for JSON or XML payloads).

### Validation

- **Built-in Attributes**: ASP.NET Core provides attributes from the `System.ComponentModel.DataAnnotations` namespace to define validation rules.
  - Examples: `[Required]`, `[StringLength]`, `[Range]`, `[RegularExpression]`.
- **Custom Validators**: Developers can create custom validators for scenarios where built-in attributes are insufficient.

## Code Example

Here's a complete example demonstrating model binding and validation in an ASP.NET Core application:

```csharp
// Model class with validation attributes
public class Product
{
    [Required]
    public string Name { get; set; }

    [Range(0, 1000)]
    public decimal Price { get; set; }
}

// Controller action handling the POST request
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    [HttpPost]
    public IActionResult Create(Product product)
    {
        // With [ApiController], invalid models automatically result in a 400 response.
        // At this point, ModelState is valid and the product can be processed.

        // Process the valid product object (e.g., save to database)
        return Ok("Product created successfully.");
    }
}

// Example of a client-side POST request payload:
/*
{
  "name": "Laptop",
  "price": 999.99
}
*/
```

### Explanation

- **Model Binding**: When the client sends a JSON payload in the request body, ASP.NET Core automatically maps it to the `Product` model.
- **Validation**:
  - The `[Required]` attribute ensures that the `Name` property is not null or empty.
  - The `[Range(0, 1000)]` attribute validates that the `Price` is between 0 and 1000 (inclusive).
  - If any validation rule fails, `ModelState.IsValid` will be `false`, and error details can be retrieved from `ModelState`.

## Common "Gotchas"

1. **Ignoring Validation Results**:
   - Failing to check `ModelState.IsValid` before processing the model can lead to invalid data being used.
2. **Overlooking Custom Validators**:
   - Developers might rely solely on built-in attributes, missing edge cases that require custom validation logic.
3. **Mismatched Data Types**:
   - Sending a non-numeric value for a numeric property (e.g., `Price`) can cause binding failures or unexpected behavior.
4. **Client-Side vs Server-Side Validation**:
   - Relying solely on client-side validation is insecure, as malicious users can bypass it. Always validate data server-side.

## Opinionated Advice

- **Always Validate Input**: Never trust user input—validate all data before processing.
- **Use Built-in Attributes First**: Start with standard attributes like `[Required]` and `[Range]`. Only create custom validators when necessary.
- **Separate Validation Logic**: Keep validation logic clean by using attributes or dedicated validator classes, rather than embedding it in controller actions.
- **Test Edge Cases**: Validate scenarios where data might be missing, malformed, or outside expected ranges.

## When Defaults Are Enough

- **Simple Forms with Basic Validation**:
  - For straightforward forms that require basic validation (e.g., required fields and numeric ranges), the default model binding and built-in attributes are sufficient.
- **No Custom Logic Needed**:
  - If your application doesn't require complex validation rules or custom logic, sticking to defaults avoids unnecessary complexity.

## Boundaries of Overkill

1. **Overengineering Validation**:
   - Adding excessive layers of validation (e.g., duplicate checks in both client-side and server-side) can introduce redundancy and maintenance overhead.
2. **Custom Validators for Trivial Cases**:
   - Creating custom validators for simple rules that are already covered by built-in attributes is unnecessary.
3. **Ignoring Performance Implications**:
   - Overly complex validation logic (e.g., expensive database queries in a validator) can degrade performance, especially in high-traffic applications.

## Summary

Model binding and validation are essential tools in ASP.NET Core for handling user input effectively. Model binding simplifies the process of mapping request data to structured objects, while validation ensures that data meets defined criteria before processing. By leveraging these features correctly, developers can build robust, secure, and maintainable web applications.

### Take-home Value

1. **Model Binding Automates Data Mapping**: Use it to convert raw HTTP requests into meaningful model objects.
2. **Validation Ensures Data Integrity**: Always validate input using attributes or custom validators before processing.
3. **Check `ModelState.IsValid`**: Never skip validation; treat invalid data as an error.
4. **Start with Built-in Attributes**: Only create custom validators when necessary.
5. **Separate Validation Logic**: Keep validation clean and maintainable by avoiding cluttered controller actions.
6. **Test Edge Cases**: Validate all possible scenarios to ensure robustness.
