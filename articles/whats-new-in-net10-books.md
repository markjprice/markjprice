**What's New in your .NET 10 books?**

The .NET 10 editions of my quartet of .NET books cover the following new features in C# 14 and .NET 10.

- [C# 14 and .NET 10 - Modern Cross-Platform Development Fundamentals](#c-14-and-net-10---modern-cross-platform-development-fundamentals)
  - [Chapter 5 - Building Your Own Types with Object-Oriented Programming](#chapter-5---building-your-own-types-with-object-oriented-programming)
    - [Partial members](#partial-members)
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

# Real-World Web Development with .NET 10


# Apps and Services with .NET 10


# Tools and Skills for .NET 10

