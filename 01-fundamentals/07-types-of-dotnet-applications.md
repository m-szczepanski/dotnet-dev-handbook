# 07. Types of .NET Applications

## 1. What problem does this solve?

New developers often struggle to understand the variety of application types they can build with .NET, leading to confusion about which type is best suited for their needs.

## 2. Core concept (simple explanation)

.NET supports a wide range of application types, from simple console apps to complex web services and desktop UIs. Understanding these options helps developers choose the right approach for their projects.

## 3. How it works (under the hood)

- Each type of .NET application uses different frameworks and tools:
  - Console apps rely on `System.Console` for input/output.
  - Web applications use ASP.NET Core for HTTP handling and routing.
  - Desktop apps leverage UI frameworks like WPF or WinForms.

## 4. Practical examples

```csharp
// Example: Simple console app
using System;

class Program
{
    static void Main()
    {
        Console.WriteLine("Hello, World!");
    }
}

// Example: ASP.NET Core Web API
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        return Enumerable.Range(1, 5).Select(index => new WeatherForecast
        {
            Date = DateTime.Now.AddDays(index),
            TemperatureC = Random.Shared.Next(-20, 55)
        });
    }
}

// Example: WPF Desktop app (XAML)
<Window x:Class="WpfApp.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml">
    <Grid>
        <TextBlock Text="Welcome to WPF!" FontSize="24" HorizontalAlignment="Center" VerticalAlignment="Center"/>
    </Grid>
</Window>
```

Explanation:

- Console apps are simple and text-based.
- Web APIs handle HTTP requests and responses.
- Desktop apps provide a graphical user interface.

## 5. Common mistakes & pitfalls

- **Overcomplicating choices**: Beginners sometimes overthink which type of application to build, when simpler options (like console apps) might suffice for learning or prototyping.
- **Ignoring platform requirements**: Developers may choose the wrong framework for their target environment (e.g., WPF on Linux).

## 6. Best practices & recommendations

- Start with simple applications like console apps to learn core concepts before moving to more complex types.
  
  ```csharp
  // Good practice: Begin with a console app for learning
  using System;

  class Program
  {
      static void Main()
      {
          Console.WriteLine("Start here!");
      }
  }
  ```

- Choose the right framework based on your project's requirements (e.g., ASP.NET Core for web, WPF for desktop UI).

## 7. When NOT to use this

- Don't build a complex desktop application if you only need a simple script or utility.

## 8. Summary

- Console apps: Simple text-based applications.
- ASP.NET Core: Web APIs and MVC applications.
- Desktop apps (WPF, WinForms): Graphical user interfaces.
- Background services: Long-running processes without UI.
- Libraries: Reusable code packages.
