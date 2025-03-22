**What's New in your .NET 10 books?**

The .NET 10 editions of my quartet of .NET books cover the following new features in C# 14 and .NET 10.

- [C# 14 and .NET 10 - Modern Cross-Platform Development Fundamentals](#c-14-and-net-10---modern-cross-platform-development-fundamentals)
  - [Chapter 5 - Building Your Own Types with Object-Oriented Programming](#chapter-5---building-your-own-types-with-object-oriented-programming)
    - [Partial members](#partial-members)
  - [Chapter 1 - Hello, C#! Welcome, .NET!](#chapter-1---hello-c-welcome-net)
    - [Solution Evolution - `.sln`, `.slnx`, and `.slnf`](#solution-evolution---sln-slnx-and-slnf)
- [Real-World Web Development with .NET 10](#real-world-web-development-with-net-10)
- [Apps and Services with .NET 10](#apps-and-services-with-net-10)
- [Tools and Skills for .NET 10](#tools-and-skills-for-net-10)


# C# 14 and .NET 10 - Modern Cross-Platform Development Fundamentals

## Chapter 5 - Building Your Own Types with Object-Oriented Programming

### Partial members

Over the past 20 years, C# has added features that allow partial types and members. The `partial` keyword can decorate a type like `class`, `struct`, or `interface` to split its implementation between multiple files. This feature was most commonly used by code-generating user interface designers in Visual Studio and other code editors. More recently, source generators use this feature to achieve similar goals.

The `partial` keyword can decorate the following members:
- Methods
- Properties
- Indexers
- Events
- Instance constructors

But with C# 14 and earlier the `partial` keyword *cannot* decorate the following members:
- Static constructors
- Finalizers
- Overloaded operators
- Delegates
- Enums

Feature|Version|Year
---|---|---
Partial Types|C# 2|2005
Partial Methods|C# 3|2007
Partial Properties|C# 13|2024
Partial Events, Instance Contructors, `field` keyword|C# 14|2025

> **Note**: The `field` keyword was a preview feature in C# 13. You must target .NET 9 and set the `<LangVersion>` element to `preview` in your project file in order to use the `field` contextual keyword. In C# 14 and later, the `field` keyword is available as standard.

> **Warning!** Partial properties, indexers, and events can't use auto-implemented syntax for the implementing declaration because the defining declaration uses the same syntax.

Learn more from the Microsoft Learn documentation:
- [Partial member (C# Reference)](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/partial-member)
- [Partial Classes and Members (C# Programming Guide)](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)

*C# Language Specification*: The language specification is the definitive source for C# syntax and usage.
- [15.2.7 Partial type declarations](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#1527-partial-type-declarations)
- [15.6.9 Partial methods](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/language-specification/classes#1569-partial-methods)
- [Partial properties](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-13.0/partial-properties)
- [Partial Events and Constructors](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/partial-events-and-constructors)

## Chapter 1 - Hello, C#! Welcome, .NET!

### Solution Evolution - `.sln`, `.slnx`, and `.slnf`

Visual Studio allows multiple projects to be grouped and opened together using a solution file `.sln`. The format of this file is a custom plain text file. In *Chapter 1*, the reader creates a solution file that references two projects. If the reader opens the solution file, it would look like the following:
```yaml
Microsoft Visual Studio Solution File, Format Version 12.00
# Visual Studio Version 17
VisualStudioVersion = 17.14.35906.104
MinimumVisualStudioVersion = 10.0.40219.1
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "HelloCS", "HelloCS\HelloCS.csproj", "{3C6C0D9B-6823-1380-8FCF-FBF0821511A6}"
EndProject
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "AboutMyEnvironment", "AboutMyEnvironment\AboutMyEnvironment.csproj", "{3F7A8DB0-B156-11EA-3DD5-F4C79E02563B}"
EndProject
Global
	GlobalSection(SolutionConfigurationPlatforms) = preSolution
		Debug|Any CPU = Debug|Any CPU
		Release|Any CPU = Release|Any CPU
	EndGlobalSection
	GlobalSection(ProjectConfigurationPlatforms) = postSolution
		{3C6C0D9B-6823-1380-8FCF-FBF0821511A6}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{3C6C0D9B-6823-1380-8FCF-FBF0821511A6}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{3C6C0D9B-6823-1380-8FCF-FBF0821511A6}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{3C6C0D9B-6823-1380-8FCF-FBF0821511A6}.Release|Any CPU.Build.0 = Release|Any CPU
		{3F7A8DB0-B156-11EA-3DD5-F4C79E02563B}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{3F7A8DB0-B156-11EA-3DD5-F4C79E02563B}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{3F7A8DB0-B156-11EA-3DD5-F4C79E02563B}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{3F7A8DB0-B156-11EA-3DD5-F4C79E02563B}.Release|Any CPU.Build.0 = Release|Any CPU
	EndGlobalSection
	GlobalSection(SolutionProperties) = preSolution
		HideSolutionNode = FALSE
	EndGlobalSection
	GlobalSection(ExtensibilityGlobals) = postSolution
		SolutionGuid = {81C51CB3-C3BC-4812-84B4-E0C19A2B9EA5}
	EndGlobalSection
EndGlobal
```

This is complex and almost impossible to edit manually due to the GUIDs. 

A replacement syntax is currently in preview, uses XML, and has the `.slnx` file extension. The preceding file would look like the following markup:
```xml
<Solution>
  <Project Path="AboutMyEnvironment/AboutMyEnvironment.csproj" />
  <Project Path="HelloCS/HelloCS.csproj" />
</Solution>
```

To use the new format in Visual Studio, enable the feature in **Options**, as shown in the following screenshot:
![Enabling .slnx format in Visual Studio | Options](assets/slnx-options.png)

Then open a solution and save it as the new format, as shown in the following screenshot:
![Saving a solution using the .slnx format](assets/slnx-save-as.png)

> **Warning!** Although you can have both solution file formats in the same directory, it is recommended to only use one or the other to avoid cofusing the build tools and other humans.



# Real-World Web Development with .NET 10


# Apps and Services with .NET 10


# Tools and Skills for .NET 10

