# 04: .NET SDK vs Runtime

## 1. What problem does this solve?

Developers often confuse the .NET SDK with the .NET Runtime, leading to unnecessary installations or missing essential components for development or production environments.

## 2. Core concept

The **.NET SDK** is used during **development** to build and compile your code into an executable format. It includes tools like compilers, NuGet package management, and debugging support. The **.NET Runtime**, on the other hand, is what runs your compiled application in a production environment.

## 3. How it works

- **SDK**: Contains everything needed to build applications, including compilers for C#, F#, and VB.NET, as well as tools like `dotnet` CLI.
  - Example: When you run `dotnet build`, the SDK compiles your code into an intermediate format called Intermediate Language (IL).
- **Runtime**: Executes compiled .NET applications. It includes components like the Common Language Runtime (CLR), which handles memory management, garbage collection, and JIT compilation.

## 4. Practical examples

```csharp
// Example: Building a simple console app using SDK
using System;

class Program
{
    static void Main()
    {
        Console.WriteLine("Hello, World!");
    }
}
```

```bash
# Command to build (SDK):
dotnet build

# Command to run (Runtime):
dotnet run
```

Explanation:

- The `dotnet build` command uses the SDK to compile your code into an executable.
- The `dotnet run` command relies on the Runtime to execute the compiled application.

## 5. Common mistakes & pitfalls

- **Installing the full SDK in production environments**: Many developers mistakenly install the entire .NET SDK on production servers, which is unnecessary and increases attack surface.
- **Missing runtime components**: Deploying an app without the appropriate runtime version can lead to runtime errors or compatibility issues.

## 6. Best practices & recommendations

- Use the **SDK** only for development machines where you're building and testing applications.
- Install the **Runtime** on production servers to run compiled .NET apps efficiently.
  
  ```bash
  # Example (Windows): Installing .NET Runtime only (no SDK) using winget
  winget install --id Microsoft.DotNet.Runtime.8 -e
  ```

## 7. When NOT to use this

- Avoid installing the full .NET SDK on production machines unless you specifically need build tools (e.g., for CI/CD pipelines).

## 8. Summary

- The **SDK** is used during development for building and compiling code.
- The **Runtime** executes compiled applications in a production environment.
- Production servers only need the Runtime, not the full SDK.
