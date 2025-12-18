# 03: A Short History of .NET (Framework → Core → Modern .NET)

## 1. What problem does this solve?

Understanding the evolution of .NET helps developers make informed decisions about which version to use for their projects, especially when considering cross-platform support, performance requirements, and compatibility with existing systems.

## 2. Core concept

The history of .NET can be summarized as a journey from a monolithic, Windows-centric platform (.NET Framework) to a modular, cross-platform solution (.NET Core), culminating in the unified and modern `.NET 6+` ecosystem.

## 3. How it works

- **.NET Framework**: A complete runtime environment designed primarily for Windows, with tight integration between the framework and operating system.
- **.NET Core**: A modular, cross-platform version of .NET that decouples the runtime from specific platforms, making it more flexible and portable.
- **.NET 6+**: The unified future of .NET, combining the best features of both Framework and Core into a single ecosystem.

## 4. Practical examples

The most visible change for a developer isn't the C# code—it's the Project File (.csproj). The old way was a nightmare of XML. The new way is clean and human-readable. There are code conventions changes between versions, but as can be seen the biggest difference is in the configuration aspect.

```xml
<!-- Legacy .NET Framework Project File -->
<Project ToolsVersion="15.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <Import Project="$(MSBuildExtensionsPath)\$(MSBuildToolsVersion)\Microsoft.Common.props" />
  <PropertyGroup>
    <ProjectGuid>{A123-B456-C789}</ProjectGuid>
    <OutputType>Exe</OutputType>
    <TargetFrameworkVersion>v4.7.2</TargetFrameworkVersion>
  </PropertyGroup>
  <ItemGroup>
    <Reference Include="System" />
    <Reference Include="System.Data" />
  </ItemGroup>
  <Compile Include="Program.cs" />
  <Compile Include="Properties\AssemblyInfo.cs" />
</Project>

<!-- Modern .NET Project File -->
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net9.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>
</Project>
```

Explanation:

- In Modern .NET, we use SDK-style projects. You no longer list every file; the compiler simply "knows" that every .cs file in the folder is part of the project. This fixed 90% of merge conflicts in version control.

## 5. Common mistakes & pitfalls

- The "Core" Confusion: Developers still call it ".NET Core." While the tech is based on Core, Microsoft dropped the "Core" branding after version 3.1 to signify unification. Calling it ".NET 6/7/8/9" is the correct modern terminology.
- Targeting the Wrong Version: Using .NET Standard for new projects. Since the ecosystem is now unified, you should target the specific .NET version (e.g., net8.0) unless you specifically need to support legacy .NET Framework 4.8 clients.

## 6. Best practices & recommendations

- Always prefer LTS: For production, stick to Long Term Support (LTS) versions (like .NET 8 or .NET 10). They receive security patches for 3 years.
- Use Global Tools: Leverage dotnet tool install to get modern CLI utilities that weren't possible in the old Framework days.
- Check Compatibility: Before migrating, use the .NET Upgrade Assistant to scan for legacy APIs (like WCF or WebForms) that do not exist in modern .NET.

## 7. When NOT to use this

- Legacy Windows Forms/WPF with deep Win32 API hooks: If your app relies on specific Windows internals that haven't been ported, stay on .NET Framework 4.8.
- WCF Servers: Modern .NET does not support WCF Server (use CoreWCF or gRPC instead). If you cannot rewrite the communication layer, you cannot move to modern .NET.

## 8. Summary

- .NET Framework (1.0 - 4.8): Windows-only, monolithic, tied to the OS.
- .NET Core (1.0 - 3.1): The "reboot"—cross-platform, high performance, modular.
- .NET 6+: The unification of Core, Mono (Xamarin), and Framework.
- Project Files: Moved from messy XML to clean, SDK-style files.
- Deployment: Shifted from "Global installation" to "Side-by-side" or "Self-contained."
- Performance: Modern .NET is significantly faster than Framework (often 2x-10x for web workloads).
