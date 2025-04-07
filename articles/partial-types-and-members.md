**Breaking Apart and Reassembling C# Classes**

- [Introducing Partial Classes and Partial Members](#introducing-partial-classes-and-partial-members)
  - [Historical Timeline of Partial Features](#historical-timeline-of-partial-features)
- [Partial Classes – One Class, Multiple Files (C# 2)](#partial-classes--one-class-multiple-files-c-2)
  - [Under the hood](#under-the-hood)
- [Partial Methods – Extensibility Hooks in Generated Code (C# 3)](#partial-methods--extensibility-hooks-in-generated-code-c-3)
  - [Original restrictions](#original-restrictions)
  - [C# 9 update](#c-9-update)
  - [Example – Optional Notification Hook](#example--optional-notification-hook)
- [Partial Properties (and Indexers) – Split Properties with Backing Fields (C# 13)](#partial-properties-and-indexers--split-properties-with-backing-fields-c-13)
  - [Example – Department property with validation](#example--department-property-with-validation)
  - [Important notes for partial properties](#important-notes-for-partial-properties)
- [Partial Events – Customizing Event Behavior (Planned for C# 14)](#partial-events--customizing-event-behavior-planned-for-c-14)
  - [Example – Partial event for data completion](#example--partial-event-for-data-completion)
  - [Caveats and requirements](#caveats-and-requirements)
- [Partial Instance Constructors – Building Objects in Pieces (Planned for C# 14)](#partial-instance-constructors--building-objects-in-pieces-planned-for-c-14)
  - [Why would you want a partial constructor?](#why-would-you-want-a-partial-constructor)
  - [Example – Conditional setup in Employee constructor](#example--conditional-setup-in-employee-constructor)
- [Conclusion](#conclusion)


# Introducing Partial Classes and Partial Members

When building projects in C#, you may encounter scenarios where you want to split the definition of a class and its members across multiple files. This is exactly what **partial classes** and **partial members** allow you to do. 

C# lets you declare a class (or struct, interface, or record) in pieces, and similarly split certain member definitions (methods, properties, events, constructors) into separate parts. The compiler then merges these pieces into one complete definition at build time.

This feature is especially helpful for keeping **automatically generated code** separate from hand-written code, for large classes that multiple developers work on simultaneously, or for injecting platform-specific logic. If you're already comfortable with normal (non-partial) classes and members, this article will show you how partial features work and why they’re useful.

We'll explore each type of partial feature in C# – from partial classes and partial methods that where introduced early in C#'s life, to properties, events, and constructors that are new in C# 13 and C# 14. 

And just for fun (and clarity), the code examples will borrow characters and concepts from the TV series *Severance*. Think of a partial class like a *Lumon Industries* employee with a split personality: the “innie” part (at work) and the “outie” part (outside work) live in separate worlds but together make one person. Similarly, partial classes let different pieces of a class live in different files but unite as one class when compiled, or in Severage terms, reintegrated.

## Historical Timeline of Partial Features

Partial types and members have been introduced gradually over C#’s history. The table below outlines each partial feature and when it appeared or is expected to appear in the C# language:

| **Partial Feature**            | **Introduced in**            | **Notes**                                                        |
|-------------------------------|------------------------------|------------------------------------------------------------------|
| **Partial classes (types)**   | C# 2 (2005, .NET 2.0)       | First allowed splitting class/struct/interface definitions ([C Sharp 2.0 - Wikipedia](https://en.wikipedia.org/wiki/C_Sharp_2.0)). Widely used by Visual Studio designers and tools. |
| **Partial methods**           | C# 3 (2007, .NET 3.5)       | Allowed splitting method declaration and implementation ([C Sharp 3.0 - Wikipedia](https://en.wikipedia.org/wiki/C_Sharp_3.0#:~:text=Partial%20methods%20allow%20code%20generators,7)). Initially only `private` void methods without outputs could be partial (no implementation required). |
| **Partial methods (enhanced)** | C# 9 (2020, .NET 5)        | Restrictions lifted: now partial methods can have return values and access modifiers if an implementation is provided. Enabled more powerful use with source generators. |
| **Partial properties & indexers** | C# 13 (2024, .NET 9)    | Allowed splitting property (or indexer) definition and accessor logic, similar to partial methods ([Partial type - C# reference](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/partial-type#:~:text=Beginning%20with%20C,be%20defined%20as%20partial%20members)). Primarily intended to support source generators (e.g. auto-implemented `INotifyPropertyChanged`). |
| **Partial events** | *Planned* C# 14 (2025, .NET 10) | Will allow splitting event declaration and add/remove implementation ([Partial Events and Constructors](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/partial-events-and-constructors)). Fills a gap for scenarios like custom event wiring (e.g. weak events). |
| **Partial instance constructors** | *Planned* C# 14 (2025, .NET 10) | Will allow splitting a constructor signature and its body [More partial members](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-14#more-partial-members). Useful for generated code that needs to inject behavior in object initialization. |

*Table:* Partial class/member features by C# version

As the table shows, partial **classes** were the first to arrive (back in C# 2), and over time the concept was extended to **methods**, then much later to **properties/indexers** (in the C# 13 timeframe), and now **events** and **constructors** are being added to the family. 

> **More details in the official documentation**:
> - [The history of C#](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-version-history)

Next, let's dive into each of these in detail, see how they work, and look at examples from a (fictional) Lumon Industries codebase.

# Partial Classes – One Class, Multiple Files (C# 2)

A *partial class* is a class whose definition is split into two or more files. Each file has a `partial class ClassName` with some of the class's members, and together they form the complete class. 

Partial classes were introduced in C# 2 primarily to make it easier to manage large classes and to integrate code generation tools. For example, Visual Studio’s Windows Forms designer generates one part of a form class (with all the UI control initialization code) in an auto-generated file, and you write your event-handling code in another file – both partial class definitions merge into one class at compile time. This way, you don’t have to touch the designer’s auto-generated code (and it won’t overwrite your changes when regenerated).

Key benefits of partial classes include:

- **Organizing large or multi-faceted classes:** You can break a big class into logical pieces (e.g., UI code vs. business logic) across separate files, making it easier to navigate and edit.
- **Multiple developers** can work on different parts of the same class simultaneously without merge conflicts because each works in a different file.
- **Code generation** scenarios: A tool or source generator can create part of a class, and you can extend it by writing the other part, without inheriting or trying tricky workarounds.

All parts of a partial class must be in the **same namespace, assembly, and module**, have the same accessibility (all `public` or all `internal`, etc.), and use the `partial` keyword in their declaration. The compiler combines them, including merging any attributes and interface implementations from each part into one unified class.

**Example – Splitting “Innie” and “Outie” Behavior:**  
In *Severance*, each employee has two personas – an “innie” at work and an “outie” outside. We can model a severed employee as a single class split into two partial definitions, one for work-related behavior and one for outside-work behavior, as shown in the following code:

```csharp
// File: SeveredEmployee.Innie.cs
public partial class SeveredEmployee
{
  public void WorkOnProject()
  {
    Console.WriteLine($"{Name} is crunching numbers in the office.");
  }
}

// File: SeveredEmployee.Outie.cs
public partial class SeveredEmployee
{
  public void EnjoyFreeTime()
  {
    Console.WriteLine($"{Name} is enjoying life outside of Lumon.");
  }
}

// File: SeveredEmployee.Shared.cs
public partial class SeveredEmployee
{
  public string Name { get; }

  public SeveredEmployee(string name)
  {
    Name = name;
  }
}

// Usage
SeveredEmployee mark = new("Mark S.");
mark.WorkOnProject();
mark.EnjoyFreeTime();

// Expected output:
// Mark S. is crunching numbers in the office.
// Mark S. is enjoying life outside of Lumon.
```

In the above example, `SeveredEmployee` is defined in multiple `.cs` files. One file defines the innie’s `WorkOnProject()` method, another defines the outie’s `EnjoyFreeTime()` method, and a third defines a constructor and a shared `Name` property (you could also put shared members in one of the existing files). 

When compiled, these get combined into one `SeveredEmployee` class with all members. The code that uses `SeveredEmployee` can call both `WorkOnProject()` and `EnjoyFreeTime()` on the same object, as if they were all defined in one place. This separation could help, for instance, if one part of the class is auto-generated by a tool (imagine Lumon’s system generating the work-related methods) and another part is written by a developer.

## Under the hood
The C# compiler simply concatenates the definitions (checking that nothing conflicts). If two partial parts define a member with the same signature, that’s a compile error – you can’t have duplicates. 

But you **can** have, say, one partial file define a private field and another partial file’s methods can use it – since they become the same class, all members are unified (fields, methods, etc., regardless of which file they were in). 

This is why in the above example we could have put the `Name` property in one file and use it in methods in another file. Partial classes *do not* create two separate copies of data; there is still a single class and when you instantiate it, you get one object containing all the fields from all partial declarations.

# Partial Methods – Extensibility Hooks in Generated Code (C# 3)

Partial methods take the idea of splitting definition and implementation further, but at the **member** level. A *partial method* is a method declared with the `partial` keyword that may or may not have an implementation. 

One partial class file contains the signature (prototype) of the method, and another partial file can contain the implementation. If no implementation is provided, the compiler **silently drops** both the declaration and any calls to the method. This feature was introduced in C# 3 as a way to allow code generators to create “hook points” or extension points in classes that developers can optionally fill in.

Think of it this way: the code generator (or a framework) defines `partial void DoSomethingExtra()` in your class and perhaps calls it in some generated logic. If you need custom behavior, you write an implementation for `DoSomethingExtra()` in another file. If you don’t, the compiler just removes the empty hook and the calls to it – resulting in zero overhead. This is useful for scenarios where you might want to inject code if needed, without requiring everyone to override virtual methods or use events.

## Original restrictions 

In the initial design, partial methods had to be “invisible” if unimplemented, so C# imposed some rules. A partial method declaration **could not** have any accessibility modifiers (it was implicitly private), it **had to return `void`**, and it couldn’t have `out` parameters. It also couldn’t be `virtual` or `async` or have other modifiers. 

These rules ensured that if the method wasn’t implemented, nothing in the program would miss it (no return values needed, no interface contracts, etc.). All calls to an unimplemented partial method are removed at compile-time – they essentially compile down to nothing, so there’s no runtime cost or null reference issues.

## C# 9 update

Starting with C# 9, partial methods were *enhanced*. Now you **can** have partial methods with return types, `out` parameters, and accessibility modifiers (e.g. `public partial int CalculateScore(...)`), but in those cases an implementation *must* be provided (otherwise you get a compile error). 

In other words, C# 9 removed the above restrictions for partial methods that are intended to definitely exist. The older style (`private void partial` methods that may be omitted) is still allowed – the compiler distinguishes them by the absence of an access modifier. 

This change was made largely to empower source generators to create richer APIs. For example, a source generator can declare a `public partial bool IsMatch(string input);` method and generate the implementation elsewhere (perhaps using the `RegexGenerator` attributes introduced in .NET 7), something not possible under the old rules. For our discussion here, we’ll focus on the original pattern (void methods that may be implemented or not), as it’s the classic use case.

## Example – Optional Notification Hook

Imagine our severed employees clock in and out, and the system has an optional hook for when an innie clocks in. One part of the partial class (perhaps auto-generated) calls this hook, and if we want, we implement it in another part. If we don’t implement it, it’s as if the hook call isn’t there at all.

Let's review this example, as shown in the following code:

```csharp
// File: TimeClock.generated.cs (auto-generated)
public partial class TimeClock
{
  // Declaration of a partial method (no body here)
  partial void OnInnieClockIn(Employee employee);

  public void ClockIn(Employee employee)
  {
    Console.WriteLine($"{employee.Name} clocked in at {DateTime.Now:T}.");

    // Call the hook - will do nothing if not implemented
    OnInnieClockIn(employee);
  }
}

// File: TimeClock.Custom.cs (developer-written)
public partial class TimeClock
{
  // Implementation of the partial method
  partial void OnInnieClockIn(Employee employee)
  {
    // Custom behavior: log a special message for severed employees
    if (employee.IsSevered)
    {
      Console.WriteLine($"(Log) Innie of {employee.Name} started their shift.");
    }
  }
}

// Usage
Employee helly = new("Helly R.", isSevered: true);
Employee devon = new("Devon Scout-Hale", isSevered: false);
TimeClock clock = new();

clock.ClockIn(helly);
clock.ClockIn(devon);

// Possible output:
// Helly R. clocked in at 9:00:00 AM.
// (Log) Innie of Helena Eagon started their shift.
// Devon Scout-Hale clocked in at 9:00:01 AM.
```

In this example, the `TimeClock` class has a partial method `OnInnieClockIn`. The generated part of the class defines the method signature and invokes it inside `ClockIn`. Our custom part provides the implementation: if the employee is severed, we log an extra message (perhaps for audit purposes at Lumon). 

If we omitted the implementation file, the call `OnInnieClockIn(employee)` would simply be compiled away – meaning non-severed employees (or if the feature wasn’t needed at all) incur no extra overhead for that check. Helly, being severed, triggers the extra log line because we implemented the hook; Devon, an unsevered employee, still goes through the same `ClockIn` method but the hook does nothing (no implementation beyond the call, which vanishes).

The partial method gives us a clean extensibility point. Note we didn’t have to make `OnInnieClockIn` public or virtual or define an interface – it’s an internal hook. Only the `TimeClock` class knows about it. This pattern is common in code generation: for example, the **ORM designer** or **UI designer** could generate calls like `OnAfterSave()` or `OnLoadComplete()` that the developer can implement if needed. If not, no harm done – the calls disappear and the program runs as if they never existed.

One thing to remember: **partial methods can only be defined inside partial classes (or structs)**. You can’t have a partial method in a completely non-partial class – the class must be partial because by definition the method’s implementation lives in another part of the class. Also, partial methods (like all partial members) do not allow separate definitions to have different signatures – the declaration and implementation must match exactly (same name, parameters, generics, etc.).

# Partial Properties (and Indexers) – Split Properties with Backing Fields (C# 13)

C# 13 introduced *partial properties* (and by extension, indexers) to extend the partial pattern to property getters and setters. This feature is aimed again at scenarios with source generators or frameworks that want to inject logic into property accessors.

Prior to C# 13, if a source generator wanted to inject code when a property is accessed or modified, there weren’t great options except perhaps partial methods or rewriting IL. With partial properties, you can declare a property in one part of a partial class (essentially saying “this property exists, of this type, with these accessors”) and then implement the get and/or set logic in another part. It’s similar to partial methods: one part provides the *signature* of the property, another part provides the *body* of the accessors.

However, properties have some unique considerations:
- A property usually has a **backing field** to store its value (unless it’s computed each time). With an auto-implemented property (`public string Name { get; set; }`), the compiler generates a hidden backing field for you.
- If we split a property’s definition, we need a way for the setter and getter implementation to refer to that backing storage.

Because of this, partial properties cannot be *automatically* implemented on the defining side. In fact, the C# compiler doesn’t allow a partial property to be declared as completely auto-implemented with no body on either side – the compiler wouldn’t know if “`{ get; set; }`” was meant to be an auto-property or just a declaration that will be implemented elsewhere. 

Instead, the typical pattern is:
  - In one file, declare the property with the `partial` modifier and provide either no body or an “auto-property-like” empty body.
  - In the other file, also declare it `partial` and put the actual code in the get and/or set accessors.

To facilitate this, C# 13 introduced a new contextual keyword `field` for use inside property accessors. The `field` keyword acts like a placeholder for “the backing field of this property.” It allows you to write an implementation without explicitly declaring a private field. The compiler ensures both the defining and implementing parts are talking about the same hidden field.

> **More details in the official documentation**:
> - [Partial Classes and Members - C# | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods#:~:text=Beginning%20with%20C,could%20write%20the%20following%20code)

## Example – Department property with validation

Let’s say our `Employee` class has a `Department` property. Perhaps a source generator wants to ensure if someone is assigned to the **Macrodata Refinement (MDR)** department, a special message is logged (because MDR is… mysterious). We can declare the property in one partial file and implement the setter logic in another, as shown in the following code:

```csharp
// File: Employee.Definition.cs
public partial class Employee
{
  // Defining declaration of a partial property
  public partial string Department { get; set; }
}

// File: Employee.Implementer.cs
public partial class Employee
{
  // Implementing declaration with actual logic
  public partial string Department
  {
    get => field;  // 'field' represents the backing field for Department
    set
    {
      // Use the implicit backing field via 'field'
      field = value;
      if (value == "Macrodata Refinement")
      {
        Console.WriteLine($"[Notice] {Name} is assigned to the secretive MDR department.");
      }
    }
  }
}
```
In the preceding code, note the following:
- In `Employee.Definition.cs`, we declare `public partial string Department { get; set; }`. This looks like an auto-property, but because it's marked `partial`, the compiler treats it as incomplete – it expects to find an implementation elsewhere. 
- In `Employee.Implementer.cs`, we again declare `public partial string Department`, but this time provide bodies for get and set. We use `get => field;` and in the setter use `field = value;`. The keyword `field` is a stand-in for the compiler-generated backing field that holds the department name. The effect is that when you set `employee.Department = "Macrodata Refinement"`, the code in our partial setter runs: it assigns the value and then, if the department is `"Macrodata Refinement"`, prints the notice. If you set it to some other value, it just assigns the field with no extra output. From outside the class, `Department` behaves like a normal property.

Without partial properties, doing something like this often meant either writing the whole property yourself (no auto-property convenience) or using a partial method hack (for example, some MVVM libraries generate a `OnPropertyChanged` partial method call in property setters). Partial properties give a more natural way to express this kind of split logic. 

A real-world scenario is implementing `INotifyPropertyChanged`: a source generator could declare a partial property and implement the setter to raise `PropertyChanged` events. For example, partial properties would make certain patterns in MVVM frameworks easier.

## Important notes for partial properties

- The `field` keyword is only usable within the accessor of a **field-backed** property (which is the case here). It’s essentially a shortcut so you don’t have to declare a separate private field to store the value. You can use `field` in either or both of the accessors as needed. 

> **Warning!** With C# 13 and .NET 9, to use the `field` contextual keyword you must set `<LangVersion>preview</LangVersion>` in your project file. The plan for C# 14 and .NET 10 is that `field` keyword will be officially available without opting into preview language features.

- If you have a property with only a getter (readonly property), you could still make it partial, but then the implementing part would provide that getter’s body (perhaps computing something on the fly or retrieving from elsewhere).
- You cannot create a partial auto-property that is fully implemented by the compiler alone – at least one part must supply logic. In our example, the “defining” part’s `{ get; set; }` is not actually implementing anything; it's more like a placeholder indicating the property has both `get` and `set`. The second part had to do the work.
- The defining and implementing parts must agree on the property’s type and whether it has `get`/`set` or both (you can’t declare it as read-write in one part and read-only in another, for example).

The introduction of partial properties is primarily to **support source generators and framework code** in providing nicer APIs. For instance, the .NET team updated the Regex source generator in C# 11 and .NET 7 to use partial methods, as [I did in the 7th edition of my book](https://github.com/markjprice/cs11dotnet7/blob/main/vs4win/Chapter08/WorkingWithRegularExpressions/Program.Regexs.cs#L6). with C# 13 and .NET 9, they can offer a partial **property** if that feels more natural (e.g., a generated `partial Regex GeneratedRegex { get; }` property instead of a method). [I do this in the 9th edition of my book](https://github.com/markjprice/cs13net9/blob/main/code/Chapter08/WorkingWithRegularExpressions/Program.Regexs.cs#L6).

As a developer, you might not manually write partial properties every day, but knowing how they work helps you understand code generated by tools (and you could use them in advanced scenarios in your own libraries).

# Partial Events – Customizing Event Behavior (Planned for C# 14)

Events are another area that historically couldn’t be partial, but that’s changing. As of C# 13, events still cannot be declared `partial`. In C# 14 (planned with .NET 10), you will be able to declare *partial events*. This means you can separate an event’s declaration (the event name and delegate type) from its add/remove accessor implementations, similar to how partial properties work.

In standard C#, an event can be declared in two ways:
- *Auto-implemented (field-like) event:* e.g. `public event EventHandler SomethingHappened;`. The compiler automatically provides backing storage (a hidden delegate field) and default implementations for `add` and `remove` (they simply attach/detach the delegate).
- *Custom event:* e.g. 
  ```csharp
  private EventHandler _somethingHappened;

  public event EventHandler SomethingHappened
  {
    add 
    { 
      /* custom code, e.g., thread-safety or logging */ 
      _somethingHappened += value; 
    }
    remove 
    { 
      /* custom remove logic */ 
      _somethingHappened -= value; 
    }
  }
  ```
  This allows you to control how listeners are added/removed (for example, to use a special event manager or to restrict access). 

Partial events are aimed at enabling scenarios where a generator or library can provide those custom `add`/`remove` behaviors behind the scenes. One notable use case is **weak events**. In .NET, if an event subscriber forgets to unsubscribe, it can prevent the publisher from being garbage-collected. Weak event patterns solve this by not holding strong references to handlers. Implementing weak events manually involves a lot of boilerplate (often using `WeakReference`). With partial events, a library could provide an attribute like `[WeakEvent]` that you put on a partial event declaration, and a source generator implements the `add`/`remove` to store handlers in a weak reference list.

## Example – Partial event for data completion

Suppose in our Lumon office app, the Macrodata Refinement process raises an event when a batch of data is completed for review. We want to use a custom event mechanism (maybe to log every subscription or to handle it in a special way). We declare a partial event and implement it manually, as shown in the following code:

```csharp
// File: MacroDataRefinement.Part1.cs
public partial class MacroDataRefinement
{
  // Defining declaration of a partial event
  public partial event Action<int> DataBatchCompleted;

  public void CompleteBatch(int itemsProcessed)
  {
    Console.WriteLine($"Batch of {itemsProcessed} items processed.");
    // Raise the event (if implemented)
    OnDataBatchCompleted(itemsProcessed);
  }

  // A helper to raise the event (not strictly required, but common pattern)
  private void OnDataBatchCompleted(int count)
  {
    // In a real scenario, this might be generated or in the implementing part
    // We'll call the event invocation logic (to be provided in the other part)
    _onDataBatchCompletedHandlers?.Invoke(count);
  }
}

// File: MacroDataRefinement.Part2.cs
public partial class MacroDataRefinement
{
  // A backing field for handlers (could use a WeakEvent pattern here)
  private Action<int>? _onDataBatchCompletedHandlers;

  // Implementing declaration of the partial event
  public partial event Action<int> DataBatchCompleted
  {
    add 
    {
      Console.WriteLine("[Log] Handler subscribed to DataBatchCompleted.");
      _onDataBatchCompletedHandlers += value;
    }
    remove 
    {
      Console.WriteLine("[Log] Handler unsubscribed from DataBatchCompleted.");
      _onDataBatchCompletedHandlers -= value;
    }
  }
}
```
In the preceding code, note the following:
- In `MacroDataRefinement.Part1.cs`, we declare a partial event `DataBatchCompleted` of type `Action<int>` (meaning event handlers are methods that take an `int` parameter). There’s no add/remove implementation given here, so this is like saying “there will be an event by this name, trust me”. 
- We also have a method `CompleteBatch(int)` that does some work and then calls an `OnDataBatchCompleted(count)` helper. 
- In the `Part2.cs` file, we provide the actual event backing field and the custom add/remove. Here, whenever someone subscribes (`+=`) to `DataBatchCompleted`, our add accessor logs a message and adds the handler to a delegate field. Similarly for unsubscribe. 
- The `_onDataBatchCompletedHandlers` delegate is then invoked in `OnDataBatchCompleted` to actually notify listeners.

Using this partial event might look like as shown in the following code:
```csharp
// Usage example (not part of class):
MacroDataRefinement mdr = new();

mdr.DataBatchCompleted += count => 
  Console.WriteLine($"*** Notified: completed {count} items.");

mdr.CompleteBatch(5);

mdr.DataBatchCompleted -= null; // unsubscribing would log as well

// Expected output:
// [Log] Handler subscribed to DataBatchCompleted.
// Batch of 5 items processed.
// *** Notified: completed 5 items.
// [Log] Handler unsubscribed from DataBatchCompleted.
```

This example demonstrates a custom event implementation via partial definitions. In real life, you might not manually write the logging; instead, a generator could create that `add`/`remove` logic based on an attribute. 

For instance, if we had used `[WeakEvent] partial event Action<int> DataBatchCompleted;` in the first part, the generator could produce an implementation that stores handlers in a `WeakEventManager`. 

## Caveats and requirements

Partial events (as per the C# 14 proposal) will require that exactly one part provides the implementation and one part provides the definition – you can’t have two different implementations or two definitions for the same partial event, as described at the following link: [What's new in C# 14 | More partial members](https://learn.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-14#:~:text=More%20partial%20members). 

In practice, you’ll declare the event (with no add/remove) in one file, and implement add/remove in another. The implementing part *must* include both an `add` and `remove` block (for obvious reasons – you need both to handle subscriptions). The defining part, on the other hand, looks just like a field-like event declaration (no body). If you declare a partial event and fail to implement it anywhere, the compiler will error (unlike partial methods, there’s no “just remove it” option for events). 

Also, because of how events work, if the defining declaration uses the event in other code (like invoking it), the compiler will likely require that to be done via a helper or in the implementing part – in our example, we used a private delegate field and method to raise the event.

At the time of writing, partial events are a **planned feature**. If you try to compile the above code in C# 13 or earlier, it will not work. Support for partial events is in preview (C# 14 as part of .NET 10 previews). Once released in November 2025, you’ll be able to use them in scenarios where you need custom event semantics injected by tooling.

# Partial Instance Constructors – Building Objects in Pieces (Planned for C# 14)

The newest addition to the partial capabilities will be *partial constructors*. A **partial constructor** allows you to split the definition of a class’s constructor between different partial class files. One partial file declares the constructor signature (and parameters), and another provides the actual body. This feature is targeted for C# 14 and .NET 10 alongside partial events.

## Why would you want a partial constructor? 

One major use case is in **interop and platform invoke scenarios**. For example, Xamarin (now part of .NET for iOS/Android) can generate C# bindings for native APIs. They might want to expose a C# constructor that actually calls into a native library. With partial constructors, the binding generator can declare a constructor (with the appropriate signature and maybe attributes like `[DllImport]` or `[Export]`) and then provide the invocation code in another file. Similarly, a source generator might generate a constructor for a record or entity and let you fill in extra logic.

Another use: combining generated code with manual code in object initialization. Suppose a tool generates a class with several properties and wants to enforce that certain setup happens when the object is created, but also allows you to inject additional logic. A partial constructor can act as a hook (much like partial methods, but for object construction).

## Example – Conditional setup in Employee constructor

Let’s go back to our Employee example. Perhaps an HR tool generates a basic `Employee` class with a constructor, but we want to add some custom side effects when an employee is instantiated (like logging whether they are part of the severance program). We can simulate that with a partial constructor:

```csharp
// File: Employee.Generated.cs (generated definition)
public partial class Employee
{
  public string Name { get; }
  public bool IsSevered { get; }

  // Partial constructor declaration (no body here)
  public partial Employee(string name, bool isSevered);
}

// File: Employee.Custom.cs (our implementation)
public partial class Employee
{
  // Implementation of the partial constructor
  public partial Employee(string name, bool isSevered)
  {
    Name = name;
    IsSevered = isSevered;
    if (isSevered)
    {
      Console.WriteLine($"{Name} has undergone the severance procedure.");
    }
    else
    {
      Console.WriteLine($"{Name} is not severed (outside knowledge intact).");
    }
  }
}

// Usage
Employee mark = new("Mark", true);
Employee devon = new("Devon", false);

// Expected output:
// Mark has undergone the severance procedure.
// Devon is not severed (outside knowledge intact).
```

In `Employee.Generated.cs`, we define the class properties and declare a constructor `public partial Employee(string name, bool isSevered);` but with no body. This could represent code generated from a template (maybe based on a database schema or an API). 

In `Employee.Custom.cs`, we write the body of that constructor: assign the fields and do some extra logging. When `new Employee("Mark", true)` is called, it actually runs the code in our partial constructor implementation. The benefit is that the generated file didn’t need to know anything about our extra logging logic, and if the generator regenerates the class (say, we add a new field), our custom code remains separate.

Some points about partial constructors per the proposal:
- Only *instance* constructors (including primary constructors of record/class) can be partial. Static constructors wouldn’t make much sense to split (and there can be only one static `ctor` anyway).
- There must be exactly one defining declaration and one implementing declaration for the constructor – you can’t have two partial implementations of the same `ctor`.
- The implementing part is the only one that can have a constructor initializer (i.e., call `: base(...)` or `: this(...)`). The defining part just provides the parameter list.
- You could have multiple constructors in a class and make some of them partial and some normal, or multiple partial ones (each would need its own implementation).
- If a partial constructor is declared but not implemented anywhere, that’s a compile error (unlike old-school partial methods, you can’t leave a constructor without implementation). After all, what would it mean to call a constructor that doesn’t exist? So the expectation is a generator and a developer (or two generators) coordinate to always supply the implementation for any declared partial `ctor`.

As of now, partial constructors are also a **preview** feature. You would need the .NET 10 SDK and C# 14 preview enabled to try it. It’s an exciting addition because it rounds out the language: methods, properties, events, and constructors can all be partial in C# 14, providing a very flexible toolkit for code generation and large-scale project organization.

> **More details in the official documentation**:
> - [Partial type - C# reference | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/partial-type)

# Conclusion

Partial classes and partial members are powerful features for organizing and generating code in C#. They allow a kind of separation-of-concerns not just at a design level but at the source file level: different concerns of one class can live in different files and be stitched together by the compiler. We saw how partial classes (introduced in 2005) enable splitting a class like *Severance* splits personalities – useful for code generators and team collaboration. 

Partial methods came next, offering hooks that can be implemented or ignored with zero cost if not needed. Fast forward to the mid-2020s: partial properties/indexers were added to support more advanced source generator scenarios (like injecting code into property accessors), and the upcoming C# 14 release extends partial members to events and constructors, completing the picture.

It’s worth noting that while partial features are extremely useful in certain scenarios, you should use them when they make sense. In many cases, regular classes and members are perfectly fine and simpler to understand. Partial classes shine when there's generated code involved or a very large class to organize. Partial methods and others are usually used by code generation (you might encounter them in auto-generated files or advanced frameworks). If you do use them manually, make sure it’s for clarity or a specific need – e.g., splitting a class across files for organizational purposes, or providing an optional hook that truly can be omitted without side effects.

By understanding partial classes and members, you can better navigate large C# projects (where designers or generators have been at work) and even create cleaner APIs yourself. Just like the employees in *Severance* eventually discover, sometimes having a clear separation can be extremely useful – as long as you remember that in the end, it all comes together to form a single whole!

The official documentation provides more examples and rules for using the `partial` keyword in types and members.
- [Microsoft Learn – **Partial Classes and Methods**](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)
- [Microsoft Learn - **Partial type (C# Reference)**](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/partial-type)

