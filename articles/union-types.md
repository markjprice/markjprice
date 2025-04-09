**Discriminated Union Types in C#**

- [What Are Discriminated Unions (and Why We Need Them)](#what-are-discriminated-unions-and-why-we-need-them)
  - [Why are union types needed?](#why-are-union-types-needed)
- [Union Types in Other Languages: F# and TypeScript](#union-types-in-other-languages-f-and-typescript)
- [Discriminated Unions in C#: Current State and Native Options](#discriminated-unions-in-c-current-state-and-native-options)
  - [Classic Approach – Class Hierarchies + Pattern Matching](#classic-approach--class-hierarchies--pattern-matching)
  - [Using Tuples or Structs as Discriminated Containers](#using-tuples-or-structs-as-discriminated-containers)
- [Union Types via Libraries: ErrorOr, OneOf, FluentResults, LanguageExt](#union-types-via-libraries-erroror-oneof-fluentresults-languageext)
  - [OneOf: General-Purpose Union Types](#oneof-general-purpose-union-types)
    - [Code Example](#code-example)
    - [Pros](#pros)
    - [Cons](#cons)
    - [Summary](#summary)
  - [ErrorOr: Result/Error Union with Functional Flavor](#erroror-resulterror-union-with-functional-flavor)
    - [Code Example](#code-example-1)
    - [Pros](#pros-1)
    - [Cons](#cons-1)
    - [Summary](#summary-1)
  - [FluentResults: A Comprehensive Result Type](#fluentresults-a-comprehensive-result-type)
    - [Code Example](#code-example-2)
    - [Pros](#pros-2)
    - [Cons](#cons-2)
    - [Summary](#summary-2)
  - [LanguageExt: Discriminated Unions via Code Generation and Functional APIs](#languageext-discriminated-unions-via-code-generation-and-functional-apis)
    - [Code Example (Either and Option)](#code-example-either-and-option)
    - [Code Example (Custom Unions via \[Union\])](#code-example-custom-unions-via-union)
    - [Pros](#pros-3)
    - [Cons](#cons-3)
    - [Summary](#summary-3)
- [Comparison](#comparison)
  - [Performance and Memory Summary](#performance-and-memory-summary)
  - [Choosing an Approach: Union Types vs Tuples (and Beyond)](#choosing-an-approach-union-types-vs-tuples-and-beyond)
- [Conclusion](#conclusion)


# What Are Discriminated Unions (and Why We Need Them)

**Discriminated unions** (also called *union types*, *tagged unions*, or *sum types*) let a variable or result be *one of a limited set of types*, with the compiler tracking which case is in use​. In other words, a discriminated union type defines several *named cases*, each of which may carry different data, and any value of that union is *exactly one* of those cases. This provides a type-safe way to represent **heterogeneous data** or **multiple outcomes**. Many programming tasks naturally fit this pattern, but without union types, developers must resort to workarounds that are less safe or less clear. 

> **More Information**: [What are Discriminated Unions?](https://github.com/louthy/language-ext/wiki/What-are-Discriminated-Unions%3F) and [Discriminated Unions in F#](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/discriminated-unions#:~:text=Discriminated%20unions%20provide%20support%20for,to%20represent%20tree%20data%20structures).

## Why are union types needed?

Consider a function that sometimes returns a valid result and other times an error. In C#, you might throw exceptions for error cases or return a special status code. Neither approach is ideal: exceptions for expected outcomes are costly and not reflected in the method signature, while status codes or flags rely on conventions and require extra boilerplate. Discriminated unions offer a better way by letting the function return a *single strongly-typed union* that *encompasses both success and error cases*. The caller is then forced to handle both cases, and the compiler can help ensure no case is forgotten.

Real-world use cases abound. For example, an e-commerce API might need to return `OrderPlaced` or `OutOfStock` or `PaymentDeclined` from the same method. With a union type, the method’s return type itself clearly communicates all possible outcomes (much like an `enum` plus data for each case), and callers must handle each outcome explicitly. 

Another use case is representing shapes in a geometry library: a shape could be a `Circle`, `Rectangle`, or `Triangle`, each with different properties. A discriminated union `Shape` with cases `Circle(radius)`, `Rectangle(width, height)`, and so on, can replace a class hierarchy and ensure that switching on shape type is *exhaustive* at compile time. 

Unions are also useful for **heterogeneous collections** or **configuration options** – anywhere you need a variable that could take one of a few forms. Without unions, developers often end up using a common base class or `object` and then downcasting, or using parallel flags to indicate which flavor is present. These approaches are error-prone. For instance, using a base class requires a default case in pattern matching because new subclasses could be introduced, and using `object` means losing compile-time type checking​.

> **More Information**: [Type Unions for C# - Motivation](https://github.com/dotnet/csharplang/blob/main/proposals/TypeUnions.md#:~:text=When%20developing%20software%20you%20may,to%20represent%20at%20that%20moment), [2](https://github.com/dotnet/csharplang/blob/main/proposals/TypeUnions.md#:~:text=You%20might%20think%20you%20can,let%20it%20be%20just%20anything)

In short, discriminated unions bring clarity, safety, and robustness to scenarios that would otherwise rely on discipline and documentation.

# Union Types in Other Languages: F# and TypeScript

Discriminated unions have been proven in many languages. C#’s own sister language F# has them as a core feature. In F#, you can define a union with a succinct syntax and pattern-match on it conveniently. For example, an F# definition of a shape union might look like:

```fs
type Shape = 
    | Circle of radius: float 
    | Rectangle of width: float * height: float 
    | Triangle of base: float * height: float
```

This defines a type `Shape` that can be a `Circle`, `Rectangle`, or `Triangle`, each carrying different data (the circle’s radius, the rectangle’s width/height, and so on). The F# compiler enforces that any pattern match on `Shape` considers all cases, so you can’t accidentally ignore (say) the `Triangle` case – a key benefit of union types. The .NET documentation describes how discriminated unions “provide support for values that can be one of a number of named cases… useful for data that can have special cases, including valid and error cases, and as an alternative for small object hierarchies”​. In F#, if you had a function that might return either a result or an error, you could define a union like `Result<'T> = Success of 'T | Error of string` and the consumer would handle both `Success(x)` and `Error(msg)` explicitly. This explicit modeling of outcomes makes code self-documenting and safer. 

TypeScript offers union types as well, using a different approach. Rather than nominal cases, TypeScript unions are *type compositions*. For instance, in TypeScript you can write:
```ts
function formatInput(input: string | number) {
  if (typeof input === "string") {
    console.log(input.toUpperCase());        // input is string here
  } else {
    console.log(input.toFixed(2));           // input is number here
  }
}
```

Here `input: string | number` is a union type indicating `input` can be a `string` or a `number`. The TypeScript compiler will enforce that you only use `input` in ways valid for all members of the union, or that you narrow the type with runtime checks (like the `typeof` checks above) before using features of a specific union member. This is sometimes called *discriminated union* when there’s a common “tag” field, or simply union types when using structural checks. The key is that the type system knows `input` could be one of several types and requires the code to account for that. According to the TypeScript Handbook, “a union type describes a value that can be one of several types” and you use the `|` operator to separate the options​ [1](https://typescript-site-76.ortam.vercel.app/docs/handbook/advanced-types.html#:~:text=Advanced%20Types%20,boolean%20is%20the). Unions in TypeScript are often combined with *type guards* or a discriminant field to safely handle each possibility. For example, one might define:
```ts
type Success = { success: true; data: Customer }
type Failure = { success: false; error: string }
type CustomerResult = Success | Failure;
```

This TypeScript `CustomerResult` is a union of two object shapes. The discriminant here is the boolean `success` field. Reading `result.success` lets TypeScript infer which variant it is (`true` means we have `data`, `false` means we have `error`). This pattern achieves a similar goal as F#’s discriminated unions: ensuring via the type system that you consider both cases.

The takeaway is that many languages – functional and mainstream – support union types in one form or another. They allow functions to return *either A or B (or C, ...)* without sacrificing type safety. They enable modeling domains with a finite set of variants (states or cases) in a way that the compiler can check. As we’ll see, C# is catching up with these ideas.

# Discriminated Unions in C#: Current State and Native Options

As of 2025, C# does not yet have built-in discriminated union types in the language, but the desire for them is high and the design is actively being discussed by the C# team​. In fact, the C# language design notes show a proposal for “union classes” and related features that might appear in a [future version of the language​](https://www.reddit.com/r/dotnet/comments/1i2q3kx/discriminated_unions_in_c_update/#:~:text=Looks%20like%20DUs%20might%20be,team%20had%20the%20other%20day). These would allow you to declare something like the earlier `Shape` or `Result` union natively in C#. According to x, C# 14 will not have union types, but there’s indication they might arrive in C# 15 or later if all goes well​. For now, though, developers must implement the pattern manually or via libraries. 

How can we simulate discriminated unions in C# today? There are a few approaches.

## Classic Approach – Class Hierarchies + Pattern Matching

One straightforward way is to use an abstract base class (or interface) and a fixed set of subclasses to represent each union case​. For example, to model a simple `Result<T>` that can be either a success or an error, as shown in the following code:
```cs
public abstract class Result<T> { }

public sealed class Success<T> : Result<T> 
{ 
  public T Value { get; }
  public Success(T value) => Value = value;
}

public sealed class Error<T> : Result<T> 
{
  public string Message { get; }
  public Error(string msg) => Message = msg;
}
```

Now `Result<T>` has two known subtypes: `Success<T>` and `Error<T>`. You can create a `Result<int>` as `new Success<int>(42)` or `new Error<int>("Fail")`. Consuming code would typically use pattern matching to act on the result, as shown in the following code:
```cs
Result<int> result = ComputeSomething();
switch(result)
{
  case Success<int> s:
    Console.WriteLine($"Got value: {s.Value}");
    break;
  case Error<int> e:
    Console.WriteLine($"Something went wrong: {e.Message}");
    break;
  // default not strictly needed if we *know* all cases, but C# requires it.
  default:
    throw new InvalidOperationException("Unexpected result type");
}
```

This works, but has some drawbacks. The C# compiler **cannot enforce exhaustiveness** here – that `default` clause is required unless the `switch` is proven to handle all subclasses. Since `Result<T>` is a normal class, the compiler must allow for the possibility of unknown subclasses (even if you `sealed` the known ones, someone could add another in the future, or reflection could instantiate a new subtype). 

So, unlike F# or TypeScript, C# won’t warn you if you forget to handle a case. We have to rely on our own discipline or throw an exception in the default to catch unhandled cases at runtime​. Another issue is that making a closed hierarchy in C# is clunky – there’s no direct way to tell the compiler “these are all the subclasses of Result.” (The proposal for discriminated unions includes the idea of [closed hierarchies to address this](https://github.com/dotnet/csharplang/blob/main/proposals/TypeUnions.md#:~:text=You%20might%20imagine%20that%20the,implementing%20unions%20as%20class%20hierarchies)​.) 

On the positive side, this approach uses pure C# with no extra libraries. Modern C# pattern matching makes consuming such hierarchies fairly nice. You can even use C# 9+ record types for brevity:
```cs
public abstract record Shape;
public record Circle(double Radius) : Shape;
public record Rectangle(double Width, double Height) : Shape;
```

Pattern matching on records works similarly, and you can add methods like `Match` manually if desired. For example, we could add a method to `Shape` that does a type switch internally:
```cs
public abstract record Shape
{
  public T Match<T>(
    Func<Circle, T> onCircle, 
    Func<Rectangle, T> onRectangle)
  {
    return this switch
    {
      Circle c    => onCircle(c),
      Rectangle r => onRectangle(r),
      _           => throw new NotSupportedException("Unknown shape")
    };
  }
}
```

This provides a form of exhaustiveness (the `_` in the switch should never happen if we covered all known shapes). We’d still be in trouble if someone extends `Shape` later, but if `Shape` and its subclasses are internal to your assembly, you can effectively treat it as a closed union. C# doesn’t *prevent* new subclasses outside your control unless you use internal/private constructors and sealed types, but you can get close.

In terms of performance, the class hierarchy approach entails an object allocation for each value (each `Success` or `Error` is a separate object). This is similar to how exceptions work (allocating an exception object), except without the stack trace cost. A simple type test (`case Success<int> s`) is very fast – roughly comparable to an `is` check or virtual method call. Memory-wise, having separate classes means each instance carries the typical object overhead (type object pointer, etc.), but that’s usually negligible compared to the payload (e.g. the string or data inside). One advantage of this approach is that each case’s data is stored in a tight class specific to that case, so you only allocate what you need. The `Circle` has just a radius field; the `Rectangle` has width and height. By contrast, some other implementations (like a one-size-fits-all struct) might reserve space for the largest case even when holding a smaller one. The downside, again, is the lack of compile-time guarantees – nothing stops misuse like treating a `Result<int>` as a `Success<int>` without checking (you’ll just get a cast exception at runtime if you’re wrong). Also, without additional code, you don’t get convenient APIs for things like chaining computations through the union (though you could certainly add methods to do so). 

Overall, using a small class hierarchy is a decent stop-gap for single-case unions or where you want full control. It’s essentially how you’d build a discriminated union by hand. But because of the boilerplate and limitations, many .NET developers prefer to use library solutions that provide union types.

## Using Tuples or Structs as Discriminated Containers

Another simplistic approach is to use tuples (or structs) to bundle a value with a tag. For example, one could represent `Result<T>` as a tuple `(bool isSuccess, T value, string errorMessage)`. By convention, `isSuccess` tells you which of the other two fields is valid. A quick example:
```cs
// Using a ValueTuple to simulate a union of T or error string
(string Name, int? Age)? person = GetPerson(); 
// (just an example, here using a nullable tuple to indicate an optional result)
```

A more explicit pattern might be:
```cs
// Representing an operation result via tuple
(bool isSuccess, int result, string error) outcome = (true, 42, null);

// Usage
if (outcome.isSuccess)
{
  Console.WriteLine($"Got result: {outcome.result}");
}
else
{
  Console.WriteLine($"Failed: {outcome.error}");
}
```

This works, but notice that nothing stops us from accidentally setting both `result` and `error`, or neither. We rely on the convention that “if `isSuccess` is `true`, `error` is `null`; if false, `result` is ignored.” The compiler won’t enforce that. We could use nullability to help (like making `result` nullable and expecting it to be null on failure, and    `error` null on success), but again, that’s convention. In effect, we’re manually doing what a discriminated union would do – keeping a tag and a payload – but without any language or compiler support to ensure correctness. This is error-prone in larger codebases, as one might forget to check the flag or accidentally use the wrong field.

For more than two cases, you might use an `enum` as the tag and a struct with multiple fields, or even store an `object` for the payload. For example:
```cs
enum ShapeKind { Circle, Rectangle }

struct ShapeUnion
{
  public ShapeKind Kind;
  public double Radius;
  public double Width;
  public double Height;
}
```

Here, if `Kind == Circle` we read `Radius`, if `Kind == Rectangle` we read `Width` and `Height`. This is essentially how a C/C++ `union` with a tag (sometimes called a variant record) could be represented. But clearly, this is ad-hoc – nothing prevents reading `Width` when `Kind` is `Circle` except your own vigilance. You can add validation in property getters or methods, but at that point you’re writing a lot of plumbing that a proper union type would handle for you.

**Pros of tuple/struct approach**: It’s simple and has zero overhead beyond the fields themselves. A `ValueTuple` is a value type, so it lives on the stack or inlined in containing objects (no extra heap allocation). Pattern matching can even work with them to a degree (you can match on the `isSuccess` field then project others). If performance is absolutely critical and you want to avoid even a single object allocation for a union, a manually managed struct with a tag might be an option. For instance, in low-level scenarios (like interoperating with C unions or high-performance code), one could [use `[StructLayout(LayoutKind.Explicit)]` to overlay fields and mimic a true union of memory – but this only works for value types and is very limited​](https://stackoverflow.com/questions/3151702/discriminated-union-in-c-sharp#:~:text=Unions%20in%20C%20can%20be,with%20reference%20types%2C%20of). 

**Cons**: The lack of semantic enforcement is the big one. Tuples don’t document intent well – `(bool, T, string)` doesn’t tell you *why* there’s a bool and a string. It also doesn’t scale beyond trivial cases; as the number of union cases grows, the tuple becomes unwieldy (e.g., which field is valid for which `enum` member? It’s all by convention). Refactoring is hazardous: if you add a new possible outcome, you might extend the enum and add a new field, but every piece of code using that tuple is a potential point of failure if not updated. Unlike with a class hierarchy or union type, adding a new case won’t produce any compile errors in consumers; it will just be a new `enum` value that might not be handled in every switch (and C# will not warn on non-exhaustive switches over enums unless you deliberately enable certain analyzer rules).

From a performance perspective, a struct with a tag (or a tuple) can be very efficient: just a few fields usually. Accessing fields is direct. But if you start using `object` to hold variant data (for example, storing the payload in an `object` and switching on its runtime type), you’re back to type-checking and boxing overhead. In most cases, a well-designed library union (like the ones below) will give you similar or better performance with far more usability. In fact, one of the reasons to prefer a library like OneOf or an either-type is to avoid these manual patterns while still avoiding allocations. We’ll discuss performance of those in a moment – some use structs internally to be as efficient as a tuple. 

In summary, until C# supports discriminated unions natively, you can either simulate them with your own classes/records (gaining type safety and clarity at the cost of extra boilerplate and no exhaustiveness checking), or use a more primitive tuple/flag approach (gaining a bit of performance or simplicity but risking misuse). However, most intermediate and advanced C# developers today turn to popular libraries that provide discriminated union constructs. These libraries offer a spectrum of features – from very minimal “union of two types” to rich result-handling frameworks – each with pros, cons, and performance characteristics.

# Union Types via Libraries: ErrorOr, OneOf, FluentResults, LanguageExt

Over the years, the community has created numerous libraries to fill the union types gap in C#. We’ll look at four popular ones: 
- **ErrorOr**
- **OneOf**
- **FluentResults**
- **LanguageExt**

...and examine how each implements union types, with code examples, pros/cons, and performance notes for each.

## OneOf: General-Purpose Union Types

[OneOf](https://github.com/mcintyre321/OneOf) (available as the [NuGet package `OneOf`](https://www.nuget.org/packages/OneOf)) is a lightweight library by Harry McIntyre that provides F#-style discriminated unions in C#. It introduces a generic struct `OneOf<T0, T1, ... Tn>` that can hold exactly one of the specified types at a time​. 

For example, `OneOf<int, string>` can hold either an `int` or a `string`. If you assign an int to it, it becomes that int; if you assign a string, it becomes that string. Critically, OneOf provides methods to safely extract or pattern-match the value in a way that forces you to consider all types.

### Code Example

Suppose we want a function that can return `UserCreated` or two kinds of errors: `InvalidName` or `NameTaken`. Using OneOf, we can define the method as:
```cs
using OneOf;
using OneOf.Types; // (contains some predefined marker types like None, etc.)

OneOf<User, InvalidName, NameTaken> CreateUser(string username)
{
  if (!IsValid(username)) 
    return new InvalidName();

  var existing = _repo.FindByUsername(username);

  if (existing != null) 
    return new NameTaken();

  var user = new User(username);
  _repo.Save(user);
  return user; // implicitly wraps in OneOf as the User case
}
```
Here, `CreateUser` returns a `OneOf<User, InvalidName, NameTaken>`. We can return a `User` instance, or `new InvalidName()` (perhaps an empty class or record type just signaling that case), or `new NameTaken()`. OneOf uses implicit conversion operators so that returning a `User` directly is fine – it will be wrapped as the User case of the OneOf. 

On the consuming side, you *must* handle all three possibilities. The idiomatic way is to use the `Match` extension method:
```cs
OneOf<User, InvalidName, NameTaken> result = CreateUser(name);

IActionResult response = result.Match(
  user => Redirect("/home"),               // Case 1: got a User
  invalid => { 
    ModelState.AddModelError("Name", "Invalid username.");
    return BadRequest(ModelState);
  },
  taken => {
    ModelState.AddModelError("Name", "Username already taken.");
    return Conflict(ModelState);
  }
);
```

The `Match` method takes one delegate per type in the union (in order). It returns an `IActionResult` in this example, but it can return any type `T` you need by each lambda returning that type. The crucial thing is that **all three cases are handled**, so this code will compile only if we provided handlers for `User`, `InvalidName`, and `NameTaken`. 

This gives a compile-time exhaustiveness guarantee: if you add another type to the OneOf in the method signature, every `Match` on it will no longer compile until you add a handler for the new case. OneOf effectively turns “handle all cases” into a compiler-enforced requirement, much like a switch on an enum with no default.

OneOf also provides convenience properties like `IsT0`, `AsT1`, and so on, and even an `Implicit` cast if you really need to extract a value in a non-exhaustive way. But the primary usage is via `Match` (or `Switch` for void-return scenarios). Under the hood, `OneOf<A,B,C>` is implemented as a readonly struct with an internal discriminator (an int index) and fields for each of the possible values​. For example, `OneOf<User, InvalidName, NameTaken>` will have something like:
- `_index` field (0, 1, or 2 indicating which type is present)​.
- `_value0` of type `User`, `_value1` of type `InvalidName`, `_value2` of type `NameTaken`.

When you assign a `User`, OneOf sets `_index = 0` and stores the user in `_value0`. The other fields are left default. Because it’s a struct, the OneOf itself incurs no additional heap allocation – it lives wherever it’s used (stack or embedded in another object). The only allocations in our `CreateUser` example are the ones we explicitly made (`new InvalidName()`, and so on, which are class instances for the error cases, and the `User` itself). Pattern matching via `Match` is implemented by simply checking `_index` and invoking the corresponding delegate. The library uses generics and some source generator trickery to avoid writing a lot of boilerplate; but conceptually it’s doing a switch on an integer tag, just like a classic union.

### Pros

OneOf’s primary advantage is that it’s simple and type-safe. It lets you turn a design like “a result can be X or Y (or Z…)” into actual type signatures that the compiler and IntelliSense understand. It forces consumers to deal with all possibilities, which leads to more robust code (no more forgetting an error case). It also avoids using exceptions for control flow (which, aside from being hard to follow, are very slow)​. Instead of throwing an exception for an expected scenario (like “user name is taken”), you return a typed result that must be handled. This often makes code more readable – you see the flow of success vs. error in the code rather than hidden in try/catch – and as a side benefit can greatly improve performance. One benchmark, for example, compared using OneOf results vs throwing exceptions in a web API scenario: handling a request via OneOf was over 20× faster and allocated far less memory than using exceptions​. The difference was on the order of 35 µs vs 700 µs per operation, and ~13 KB vs 38 KB of allocations, in favor of OneOf​. This is because exceptions in .NET are exceptionally slow when thrown frequently; a union type is just normal control flow.

From a performance standpoint, OneOf is pretty optimal:
- **Memory**: The `OneOf` struct has as many fields as union cases (plus the tag int). That means it might be a bit larger in memory than a pointer – e.g. a `OneOf<int,string>` contains an `int _value0` and a `string _value1` reference, about 8+8+4 bytes on 64-bit for the fields (plus padding). But this is often negligible. And because it’s a value type, no separate object is created for it.
- **Speed**: Pattern matching is an O(1) type-index check. There’s no reflection, no dictionary of handlers – just a straight branch. In fact, OneOf’s generated code uses a switch expression under the covers for Match. JIT can inline a lot of this, so it becomes as direct as writing it by hand.
- **JIT/code size**: OneOf uses generics, so each unique combination of types produces a specialized struct. If you use many different OneOf combinations, the JIT will generate code for each. However, the library is careful to generate most of the boilerplate via source generation to keep each instantiation small. And typically, you don’t have an explosion of different unions in one program – you might have a dozen at most, which is fine.

### Cons

OneOf’s focus on exhaustiveness and minimalism means it doesn’t provide fancy extras. For instance, it doesn’t carry metadata like a list of errors or additional context (you have to encode that in your case types if needed). Also, while `Match` is great for forcing handling, it can be slightly verbose for simple flows. Some developers prefer more fluent chaining (like `.Map` or `.Bind` functions seen in functional languages) – OneOf doesn’t directly provide those, though you can achieve similar results by writing extension methods or using LINQ expressions with OneOf. 

Another consideration: because OneOf relies on lambdas for `Match`, if those lambdas capture variables from outside, they will allocate closure objects. In practice, you can usually avoid or mitigate this (often the lambdas are simple and static). If allocation-free matching is needed, you could use manual `if`/`is` checks which one might argue loses exhaustiveness checking – though you could still call `result.IsT0`, `result.IsT1`, and so on to drive logic. OneOf does include some predefined case marker types (like `OneOf.Types.None`, `True`, `False`, and so on) to help represent common cases (maybe/option types, booleans in a union)​. 

Another minor downside: if you need to serialize a OneOf (say to JSON), it’s not immediately obvious how to represent it. The library itself doesn’t dictate a serialization format, so you might need to write a custom converter (there have been community contributions in this area). But for in-memory usage and application logic, 

### Summary 

OneOf is an excellent, low-overhead solution that many consider a go-to for adding union semantics in C#​. Its design is essentially “like a compile-time checked switch statement” – which succinctly captures how it brings the benefits of discriminated unions into C#.

## ErrorOr: Result/Error Union with Functional Flavor

ErrorOr (NuGet package `ErrorOr`) is a library that specifically models the outcome of operations that can **either succeed with a result or fail with error(s)**. It can be seen as a specialized two-case discriminated union (`T` or error), but it’s enriched with features to make error-handling more fluent. The type `ErrorOr<T>` represents either a successful result of type `T`, or one or more `Error` objects describing what went wrong. This is essentially the *Result pattern* implemented as a union type – similar to how you might use `Either<Error, T>` in other languages.

### Code Example

Using ErrorOr looks like this in practice:

```cs
using ErrorOr;

ErrorOr<double> Divide(double a, double b)
{
  if (b == 0)
    return Error.Unexpected(description: "Cannot divide by zero");
  return a / b;
}

// Consuming the result:
var result = Divide(4, 2);
if (result.IsError)
{
  Console.WriteLine(result.FirstError.Description);
}
else
{
  Console.WriteLine($"Answer: {result.Value}");
}
```

In this snippet, `Divide` returns an `ErrorOr<double>`. On error, we return an `Error` (provided by the library via `Error.Unexpected(...)` factory). On success, we just return the double result, which implicitly converts into an `ErrorOr<double>` carrying a value. The consumer then checks `result.IsError` to decide what to do. If there’s an error, `result.FirstError` gives the first error object (you could also iterate `result.Errors` for all of them). If not, `result.Value` contains the double.

ErrorOr also offers a fluent API so you can chain operations without deep nesting. For example:

```cs
ErrorOr<User> maybeUser = await _repo.GetByIdAsync(id);
return maybeUser
  .Then(user => user.WithIncrementedAge())    // transforms the User, or propagates error
  .Then(user => _repo.UpdateAsync(user))      // perhaps update in DB
  .Match(
    user => Results.Ok(user),                 // success case -> HTTP 200
    errors => Results.BadRequest(errors.ToProblemDetails())
  );
```

This hypothetical example uses `.Then(...)` which applies a function if the current state is a value (and carries the error state through if not). Eventually, `.Match()` (similar to OneOf’s Match) is used with two lambdas: one for the success (`user => ...`) and one for the failure (`errors => ...`). The library provides many such methods: `Then`, `Map`, `Bind` (or the equivalent via LINQ), `Else` to handle errors, `Switch` for side effects, etc., making it convenient to work with `ErrorOr<T>` in a pipelined way ([GitHub - amantinband/error-or: A simple, fluent discriminated union of an error or a result.](https://github.com/amantinband/error-or#:~:text=Or%2C%20using%20Then%2FElse%20and%20Switch%2FMatch%2C,you%20can%20do%20this)) ([GitHub - amantinband/error-or: A simple, fluent discriminated union of an error or a result.](https://github.com/amantinband/error-or#:~:text=return%20await%20_userRepository.GetByIdAsync%28id%29%20.Then%28user%20%3D,NoContent)). 

Internally, `ErrorOr<T>` typically contains either a **value** of type T or a **list of Error** objects ([GitHub - amantinband/error-or: A simple, fluent discriminated union of an error or a result.](https://github.com/amantinband/error-or#:~:text=Support%20For%20Multiple%20Errors)) ([GitHub - amantinband/error-or: A simple, fluent discriminated union of an error or a result.](https://github.com/amantinband/error-or#:~:text=if%20%28errors.Count%20,return%20errors%3B)). The `Error` type is a class that might include an error code, description, perhaps a cause, and it’s designed so that multiple errors can accumulate (e.g., validation might produce several errors at once). The `ErrorOr` is likely implemented as a struct that has, say, a `List<Error>` field and a T field, plus maybe a flag. The library makes heavy use of **implicit conversions**: you can return a T where `ErrorOr<T>` is expected (it wraps it as success), or return a single `Error` or a `List<Error>` (it wraps it as failure) ([GitHub - amantinband/error-or: A simple, fluent discriminated union of an error or a result.](https://github.com/amantinband/error-or#:~:text=public%20class%20User%28string%20_name%29%20,Error%3E%20errors%20%3D)) ([GitHub - amantinband/error-or: A simple, fluent discriminated union of an error or a result.](https://github.com/amantinband/error-or#:~:text=if%20%28errors.Count%20,return%20errors%3B)). This makes the syntax very natural – just `return new User(...)` for success or `return Error.Validation("...")` for error, and it all meshes.

### Pros

ErrorOr is great when your use case is specifically the *success-or-failure* scenario (which is extremely common in business apps and APIs). It’s basically a disciplined way to avoid using exceptions for flow control and to make error states explicit in the type system. Compared to OneOf, ErrorOr is less about arbitrary types and more about *standardizing error handling*. For example, it comes with some predefined error factories like `Error.Validation(...)`, `Error.NotFound(...)`, etc., that produce Error objects with common categories (it likely even integrates with HTTP status codes or Problem Details for APIs). The ability to carry multiple errors is a plus for scenarios like input validation where you might want to report all problems, not just the first. 

The fluent chaining API can lead to very readable code. Instead of nested if-checks or switch, you can write a linear sequence of transformations that short-circuits on error. This is effectively monadic binding (`Then` is like `Bind` in functional terms). It allows you to, for instance, parse an input, then transform it, then call another function, all the while propagating any error without having to check after each step manually. In the end, you handle the outcome in one place. This style is reminiscent of using `Task` or LINQ, but here it’s for the union type.

Performance of ErrorOr is generally good. It’s often implemented as a struct (to avoid extra allocations for the container). If it holds a value, that value is stored directly in the struct (for reference types, that’s just a pointer; for value types, it’s the value itself). If it holds errors, it likely stores a reference to a list of Error objects. So a successful `ErrorOr<int>` might be, say, 4 bytes of int plus a null reference for the error list; a failed one might be an empty default int plus a reference to a list object. Accessors like `IsError` likely just check an internal flag or whether the list is non-null. One potential overhead is that if you *do* have multiple errors, a list has to be allocated. However, single errors might be optimized (the library might avoid creating a list until there’s more than one error, perhaps by treating a single Error as a list of length one implicitly). In any case, error scenarios typically involve allocations for error objects themselves, which is expected since you want rich information (unlike an exception, though, you’re not capturing a stack trace for each, so it's cheaper).

A noteworthy performance aspect: *comparative to exceptions, `ErrorOr` will save a ton of time and memory when errors occur frequently.* We saw earlier how a OneOf vs exception benchmark yielded huge gains; `ErrorOr` would show similar benefits because it’s the same principle – using normal control flow instead of exceptions. It’s not unusual to see web projects using `ErrorOr<T>` or similar result types to handle validation and domain errors, reserving exceptions for truly unexpected issues. This can increase throughput significantly under load ([Matthew Regis - Discriminated Unions With OneOf](https://matthewregis.dev/posts/discriminated-unions-with-one-of#:~:text=Method%20Mean%20Error%20StdDev%20Min,14%20KB)).

### Cons

Because ErrorOr is centered on the success/error pattern, it’s not as general as OneOf. You wouldn’t use ErrorOr to represent three unrelated data types, for example – it always has the notion of “errors” versus “a value”. If your scenario doesn’t involve an error concept (say, a union of shapes), OneOf or a custom union is more appropriate. Also, the exhaustive handling guarantee is a bit weaker here. With OneOf, if you don’t handle a case, you can’t compile. With ErrorOr, typically you check `IsError` or call `.Match(valueFunc, errorFunc)`. It’s still up to you to handle the two cases. That said, using `.Match` will force you to handle both success and failure paths (and it’s likely implemented such that you *must* provide both an `onValue` and `onError` delegate) – so you get exhaustiveness in practice when using the provided APIs. Just be aware you could also access `.Value` directly; if you do so when `IsError` is true, the library will throw an exception to alert you (similar to how `Nullable<T>.Value` throws if null). In fact, `result.Value` in ErrorOr might throw an `InvalidOperationException` if used incorrectly, which is a little ironic (we’re avoiding exceptions, but misuse leads to one) – however, this is by design to catch logical bugs. The library provides safer alternatives like `ValueOrDefault` or the need to check `IsError` first, so it encourages the right usage.

Another con is that ErrorOr introduces its own `Error` type, which you should map to your domain or perhaps HTTP responses. Some might find it a bit heavy if all they need is a simple “string error message”. However, you can still just use `ErrorOr<T>` with simple errors (just supply messages or use the default error types). Over time, you might appreciate the consistency it brings (e.g., always having an `Error.Code` and `Description` can standardize error handling in a large system).

### Summary 

Overall, **ErrorOr is a great choice** if you are specifically implementing the *Result pattern* (also known as *Railway Oriented Programming* in some circles). It strikes a balance between **clarity** (the method signature shows `ErrorOr<Something>` so you know it can fail), **safety** (you can’t easily ignore the possibility of error), and **ergonomics** (fluent APIs to work with results). It’s a newer library compared to some others but has gained popularity especially in the .NET web dev community for modeling API outcomes.

## FluentResults: A Comprehensive Result Type

[FluentResults](https://github.com/altmann/FluentResults) is another library that approaches the union type from the perspective of **result handling**, with a design influenced by functional programming and fluent APIs. It provides a `Result` type (and `Result<T>`) that can indicate success or failure, similar to ErrorOr, but with an API that emphasizes *collecting messages (errors or successes)* and *method chaining*. FluentResults predates some of the newer libraries and is quite feature-rich, including integrations for ASP.NET.

### Code Example

Using FluentResults typically looks like:

```cs
using FluentResults;

// Producing a Result
Result<User> GetUserByEmail(string email)
{
  User? user = _repo.FindByEmail(email);
  if (user == null)
    return Result.Fail<User>("No user found with that email.");
  return Result.Ok(user);
}

// Consuming a Result
Result<User> result = GetUserByEmail("alice@example.com");
if (result.IsFailed)
{
  // gather error messages
  foreach(var err in result.Errors)
  {
    Console.WriteLine($"Error: {err.Message}");
  }
}
else 
{
  User user = result.Value;  // safe because we checked IsFailed
  Console.WriteLine($"Hello, {user.Name}");
}
```

Here, `Result<User>` can be in a *Success* state or *Failed* state. You create successes with `Result.Ok(value)` and failures with `Result.Fail<T>(message or ErrorObject)`. Notably, `Result.Fail` can take either a simple string or a rich `Error` object. FluentResults encourages using its `Error` class (and similarly a `Success` class for success messages) to encapsulate information. You can even have multiple error messages in one result. For example:

```cs
var result = Result.Fail("Invalid input")
                   .WithError("Name cannot be empty")
                   .WithError("Age must be positive");
```

This produces a `Result` that is failed and contains three error reasons. The design allows a result to carry a *list of reasons*, which can be either errors or successes (success reasons might be like informational messages). In a success scenario, you might attach a warning or info message using `.WithSuccess("cached result")` or similar. All these messages implement an interface `IReason`, and you can access `result.Reasons` for the whole collection, or specifically `result.Errors` and `result.Successes` ([FluentResults/README.md at master · altmann/FluentResults · GitHub](https://github.com/altmann/FluentResults/blob/master/README.md#:~:text=%2F%2F%20get%20all%20reasons%20why,IReason%3E%20reasons%20%3D%20result.Reasons)) ([FluentResults/README.md at master · altmann/FluentResults · GitHub](https://github.com/altmann/FluentResults/blob/master/README.md#:~:text=if%20%28result.IsFailed%29%20,in%20failed%20state%20return%3B)).

FluentResults also supports chaining: you can do `result = result.Bind(...).Bind(...)` to chain computations (the methods might be called `Tap` or `Map` or similar, depending on version). If an error occurs at any step, the chain short-circuits, and the error is carried through. It’s similar in spirit to ErrorOr’s chaining, though the naming might differ (e.g., older versions of FluentResults used `Then`/`OnSuccess` etc., recent versions might have updated names). They even provide extension methods to integrate with ASP.NET Core, so you could directly return a `Result` from a controller and have a filter convert it to an HTTP response (via an extension package) ([GitHub - altmann/FluentResults: A generalised Result object implementation for .NET/C#](https://github.com/altmann/FluentResults#:~:text=Key%20Features)) ([GitHub - altmann/FluentResults: A generalised Result object implementation for .NET/C#](https://github.com/altmann/FluentResults#:~:text=in%20an%20elegant%20way%20,NET%20Controller)).

### Pros

FluentResults is **very expressive**. It’s not just a binary union of success/failure but a whole framework to attach information to outcomes. This can be useful in complex domains. For instance, an operation might succeed but with warnings – FluentResults can model that (a success result with both `IsSuccess` true and some `Successes` messages). Or an operation might fail for multiple reasons – it can carry all of them. The library pushes you to use *object-oriented error descriptions* (by subclassing `Error` or adding metadata to it) instead of plain strings ([FluentResults/README.md at master · altmann/FluentResults · GitHub](https://github.com/altmann/FluentResults/blob/master/README.md#:~:text=Designing%20errors%20and%20success%20messages)) ([FluentResults/README.md at master · altmann/FluentResults · GitHub](https://github.com/altmann/FluentResults/blob/master/README.md#:~:text=)). This means your error handling can be richer – e.g., error objects might have error codes, references to causing exceptions, etc., enabling programmatic decisions based on error type. 

Another strength is its **fluent API**. You can create and enrich results in a single expression, which can make the code for building responses very neat. The presence of `.WithError` and `.WithSuccess` encourages accumulating context as the result bubbles up, rather than replacing it. And if you need to assert in tests that a result contains a certain error, the library even has integration with FluentAssertions to make that easy.

In terms of performance and usage, FluentResults is implemented as classes internally. For example, `Result<T>` is a class that contains a list of `IError` and possibly a value of type T ([FluentResults/README.md at master · altmann/FluentResults · GitHub](https://github.com/altmann/FluentResults/blob/master/README.md#:~:text=Creating%20a%20Result)) ([FluentResults/README.md at master · altmann/FluentResults · GitHub](https://github.com/altmann/FluentResults/blob/master/README.md#:~:text=Additionally%20a%20value%20from%20a,also%20be%20stored%20if%20necessary)). Each `Error` is likely also a class (potentially with fields for message, error code, exception, etc.). So, compared to OneOf or ErrorOr (which often use structs), FluentResults will allocate a bit more: each result is a separate object, and error messages are objects too (unless you use the string shortcut, in which case it likely wraps the string in an `Error` object behind the scenes). However, these allocations are not usually a big concern unless you’re creating millions of results per second. For typical user-driven operations, it’s fine.

One could think of FluentResults as trading a bit of performance for **maximum flexibility and clarity in error reporting**. If your app needs to log a chain of events about why something failed, the ability to keep a chain of errors (with causes) in a single Result object is very handy. It even supports linking results (an `Error` can have an `InnerReason` to form a hierarchy, akin to exception cause chains) ([GitHub - altmann/FluentResults: A generalised Result object implementation for .NET/C#](https://github.com/altmann/FluentResults#:~:text=,details)) ([FluentResults/README.md at master · altmann/FluentResults · GitHub](https://github.com/altmann/FluentResults/blob/master/README.md#:~:text=,Error)).

### Cons

The flip side of its richness is **complexity and overhead**. If you don’t need multiple errors or warnings, FluentResults might feel too heavy. For example, if a method can only really fail in one way, having a `Result<T>` with a list of errors (of length 1) is somewhat more than necessary – a simpler union or exception might do. Some critics argue that FluentResults ends up being a bit verbose (you have to check `IsFailed` and then get `.Errors`, etc., versus a simple if or exception). It also doesn’t enforce handling as strictly at compile time. It’s easy enough to ignore a `Result` (just not check `IsFailed` and proceed – then `.Value` will throw at runtime if it was an error). So you still need discipline or patterns to ensure consumers deal with results. In practice, teams establish guidelines: e.g., always check `IsSuccess` or always return up the result to a higher layer that knows how to handle it.

From a performance perspective, each `Result` is an object and contains at least a list (even if empty). The library likely reuses empty static lists for success cases (to avoid creating new empty lists each time) – that’s a common optimization. But any error means allocating an `Error` (or several). This is still far cheaper than throwing exceptions for errors (no stack trace, and you can reuse Error definitions). And because `Result` is a class, passing it around is just passing a reference (no copying of big structs). So performance is nuanced: small struct (like OneOf) vs small class (like Result) – small struct might be faster in some cases, but small class might avoid copying costs if used in many places. The JIT will treat `Result<T>` like any reference type. Also, generics: `Result<T>` will instantiate per value type T (just like others) but one nice thing is if you often use plain `Result` (non-generic) for void methods, that’s a single type reused everywhere.

Another con is that FluentResults, due to its object-oriented nature, doesn’t play as nicely with pattern matching or some newer expression-based code. It expects you to use its APIs to inspect the result. You can of course pattern match: `if (result is SuccessResult<T> suc) ...` because under the hood there are classes (perhaps `SuccessResult<T>` and `FailedResult<T>`), but that’s not documented usage and could break encapsulation. Instead, you stick to `IsFailed`, `IsSuccess`, and the provided properties.

### Summary 

In summary, **FluentResults** is a powerful toolkit for those who want a comprehensive solution to handle success/failure and log or propagate reasons. It shines in applications where understanding *why* something failed (with multiple causes) is important, or when you want to attach additional info even on success. It’s slightly less about being a pure “union type” and more about implementing a pattern (result monad) in an ergonomic way. If you only need the union aspect (one of two states) and care about absolute minimal overhead, OneOf or ErrorOr might be preferable. But if you value the richer semantics and don’t mind a bit of overhead, FluentResults is a mature solution.

## LanguageExt: Discriminated Unions via Code Generation and Functional APIs

[LanguageExt](https://github.com/louthy/language-ext) is a full-featured functional programming library for C#. It provides immutable data types, monads (Option, Either, Try, etc.), and more. Among its offerings are discriminated unions, tackled in a couple of ways. First, LanguageExt has **built-in types** that cover common union scenarios: e.g., `Option<T>` (some or none), `Either<L,R>` (left or right), `Aff<Err, Ok>` (for async effects), etc. These are analogous to union types of two cases. Second, for defining your own multi-case unions, LanguageExt offers a **code generation** approach with a `[Union]` attribute.

### Code Example (Either and Option)

If you just need an *either type*, LanguageExt’s `Either<L,R>` is ready to use. For example:

```cs
using LanguageExt;
using static LanguageExt.Prelude;  // for Right()/Left() helpers

Either<string, User> result = GetUserByName(name)
  .ToEither("User not found");

User user = result.Match(
  Right: u  => u,
  Left:  errMsg => new User("guest")  // default user if not found
);
```

Here, `Either<L,R>` is essentially a union of `L` (often used for an error or left case) and `R` (usually the success or right case). LanguageExt provides `result.Match(Right: ..., Left: ...)` similar to OneOf’s match (named parameters make it clear which lambda handles which side). You can also use LINQ query syntax to chain `Either` computations. Under the hood, `Either<L,R>` is typically a struct that either holds an L or an R (much like OneOf’s struct) – in earlier versions it was class-based, but newer LanguageExt versions favor structs for performance in many cases. The library ensures that if you try to get the Right value when it’s actually Left, you’ll get a meaningful exception or have to handle it (depending on how you access it; usually you don’t directly, you use `.Match` or `.IfRight`/`.IfLeft`).

Likewise, `Option<T>` represents a T or nothing. You use `Option<T>` as an alternative to `T?` that forces explicit handling of the None case. For example:

```cs
Option<Order> maybeOrder = FindOrder(id);
maybeOrder.Match(
  Some: order => Process(order),
  None: () => Console.WriteLine("Order not found")
);
```

LanguageExt’s `Option` and `Either` are analogous to what union types in F# or other languages provide for optional values and error-handling respectively. They come with a suite of extension methods for mapping, binding, filtering, etc., which makes them very convenient for functional-style programming. Performance-wise, these are tuned to avoid allocations (e.g., `Option<T>` is a struct with a boolean and a value, so no heap unless T itself is reference; `Either<L,R>` can be a struct with two fields and a tag, similar to OneOf as we discussed).

### Code Example (Custom Unions via [Union])

LanguageExt allows you to define your own discriminated union types with any number of cases using an attribute and interface. For instance, from the LanguageExt documentation, you can do:

```cs
[Union]
public interface Maybe<A>
{
  Maybe<A> Just(A value);
  Maybe<A> Nothing();
}
```

This single interface with two methods defines a union of two cases: one carrying an `A` (the `Just` case) and one carrying nothing (`Nothing` case) ([What are Discriminated Unions? · louthy/language-ext Wiki · GitHub](https://github.com/louthy/language-ext/wiki/What-are-Discriminated-Unions%3F#:~:text=This%20is%20obviously%20less%20convenient,CodeGen)). The LanguageExt code generation will produce the implementation for you: it will create classes (or records) that implement `Maybe<A>` for each case, including a `Just<A>` class with an `A` property, and a `Nothing<A>` class (probably a singleton or stateless class). It will also generate a `Match` function on the interface that takes two delegates (for `Just` and `Nothing`) and ensures you handle both ([What are Discriminated Unions? · louthy/language-ext Wiki · GitHub](https://github.com/louthy/language-ext/wiki/What-are-Discriminated-Unions%3F#:~:text=public%20abstract%20record%20Maybe,)). So, after the codegen (triggered at compile time via a Source Generator or the older CodeGen tool), you can use `Maybe<int>` as a type:

```cs
Maybe<int> maybeNumber = new Just<int>(10);    // or Maybe<int>.Just(10) via the interface static maybe

int result = maybeNumber.Match(
    just => just.Value * 2,
    nothing => 0
);
```

This pattern scales to more cases; if you had 3 or 4 cases, you’d put them as methods in the interface and the generator would output corresponding classes and a comprehensive `Match`. The benefit here is you get a *nominal discriminated union type* in C# with relative ease – you write an interface that *declares the shape* of the union, and let the generator fill in the boilerplate. LanguageExt’s generator uses either records or classes for the cases (likely records in newer versions, to get equality, `ToString`, etc., out of the box). It does **not** use a single struct for all cases, because with arbitrarily different payloads that can be tricky; instead, it goes the sealed class route, which is perfectly fine given the generator ensures no outsider can add cases (since the interface is internal and implemented only by generated code). The generated `Match` method or extension will enforce completeness by requiring all case handlers (similar to OneOf’s approach) ([What are Discriminated Unions? · louthy/language-ext Wiki · GitHub](https://github.com/louthy/language-ext/wiki/What-are-Discriminated-Unions%3F#:~:text=This%20is%20also%20nice%20and,function)) ([What are Discriminated Unions? · louthy/language-ext Wiki · GitHub](https://github.com/louthy/language-ext/wiki/What-are-Discriminated-Unions%3F#:~:text=So%2C%20what%20are%20they%20useful,in%20is%20a%20good%20thing)).

### Pros

LanguageExt provides *both* low-level and high-level tools for unions. If you already use LanguageExt for its other features (immutable collections, etc.), using its `Either` or `Option` means one less dependency and a consistent API. These types are well-tested and integrate with the rest of the library (e.g., you can convert an `Option` to an `Either`, or use `Try<T>` which is like an `Either<Exception,T>` for capturing exceptions). The codegen `[Union]` approach is powerful for more complex unions – you get a nicely named type with named cases without writing much code. Additionally, LanguageExt often provides additional methods like `MatchUnsafe` (for scenarios where you *assert* you know the case, to avoid delegate overhead) and a rich LINQ integration to chain computations in a query comprehension style. It’s a comprehensive FP library, so union types don’t exist in isolation – you have things like `Validation` (which is like an applicative that can collect errors), `TryOption` (combining exceptions and option), etc. This can be very powerful if you are building a large system with functional patterns.

Performance in LanguageExt is generally a focus – the author (Paul Louth) has written about struct vs class trade-offs and often chooses structs for frequently used types to minimize allocations. For example, `Either<L,R>` in v5 is a struct that avoids allocation when holding a Right or Left (except whatever that L or R is), and pattern matching it is essentially a branch on an internal tag. The codegen unions, since they produce classes for cases, will have similar performance characteristics to the hand-rolled class hierarchy approach: each union value is an object of a specific case class. That means an allocation per value. However, those case classes could be records with value-based equality, which is a nice feature (OneOf and others rely on whatever the underlying types do for equality, whereas a record case would implement IEquatable at the union level).

### Cons

The main con of LanguageExt is that it’s a large library – pulling it in for just union types might be overkill. It shines if you embrace its paradigm, but if you only want a small union, writing a few classes yourself or using a tiny library like OneOf might be simpler. There’s also a learning curve: types like `Either` and `Option` come from FP tradition, so if you’re not used to them, it might take a bit of practice to use them idiomatically (e.g., understanding that `Right` is the success by convention and that you should use `Left` for error, etc.). The `[Union]` codegen is convenient, but it adds a layer of magic – you need to set up the source generator (in newer versions this is automatic if you include the LanguageExt.CodeGen package), and if something goes wrong, debugging generated code can be tricky. That said, once set up, it’s pretty seamless.

Another consideration: completeness checking in pattern matching. If you use C#’s native pattern matching (like a `switch` on the base interface or base record), the compiler won’t know it’s closed, so it will demand a default case. You are supposed to use the provided `Match` method from LanguageExt, which *does* enforce completeness by design ([What are Discriminated Unions? · louthy/language-ext Wiki · GitHub](https://github.com/louthy/language-ext/wiki/What-are-Discriminated-Unions%3F#:~:text=This%20is%20also%20nice%20and,function)). So, similar to OneOf, you rely on the library’s matching function rather than the raw `switch` to get the benefit. This is not a real drawback, just something to be aware of in how you use it.

Performance-wise, if using the monadic style (LINQ or Bind calls), you have to consider the overhead of lambdas and maybe intermediate structs. But LanguageExt tries to minimize allocations (it often uses struct-based enumerators and such). If you go crazy with nested functional combinators, there could be some overhead, but typically it’s negligible for I/O bound or high-level logic compared to, say, network calls or database queries.

### Summary 

In summary, **LanguageExt** is ideal if you want a *full FP ecosystem* in C# – union types included. It provides the most **comprehensive set of patterns** (Option, Either, etc.) and even lets you define custom unions cleanly. The tradeoff is the added complexity and the need to align with its approach. If a project already uses LanguageExt, it’s a no-brainer to use its union capabilities instead of adding something else. If not, one might weigh the need: for a single simple union type, using LanguageExt might be like bringing a tank to a fistfight – powerful but maybe more than needed. However, many find that once they include it, they benefit from its other features as well, which can lead to more robust and declarative code overall.

# Comparison

## Performance and Memory Summary 

It’s worth summarizing the performance aspects of these approaches side-by-side, as that often influences the choice:

- **Tuples/struct with tag (manual):** No extra allocations, minimal fields. Fast, but no safety – use only if you’re in a low-level scenario and can rigorously enforce correctness (rare for application logic). The lack of built-in semantics makes it easy to introduce bugs.

- **Class hierarchy (manual or codegen like LanguageExt [Union]):** Each value is a separate object. Allocation cost for each created union value (plus any payload objects). Pattern matching requires either `if`/`is` checks or a switch, which are very quick (a type check is basically checking an internal vtable pointer). Memory usage for the object header might add ~16 bytes per value (not counting payload). If your union cases are large (e.g., big structs), you may actually prefer a class so that you don't copy big structs around; if they’re small (e.g., an int), classes add overhead. No compile-time exhaustiveness (unless you use generated Match functions). Acceptable in most cases, and straightforward.

- **OneOf (struct-based, multiple generics):** No per-value allocation overhead (the struct is usually on the stack or inline). There *is* an allocation if you box it or capture in a lambda (like passing it as `object` or capturing the whole struct in a closure), but typically you don’t need to. Memory footprint is the sum of all cases’ field sizes, so it can be larger than a single case object. For example, `OneOf<int,string>` has space for an int and a string reference; a class approach would allocate either an int wrapper or a string wrapper, but not both at once. In practice, unless you have many unused large fields, the overhead is minor. JITted code for each combination can inline a lot, so performance is excellent. OneOf’s exhaustive `Match` uses delegates, which can allocate if not careful, but you can often use lambdas without captures (which the compiler can cache or treat as static). Also, modern .NET may even avoid allocation for short-lived delegate invocations through optimizations.

- **ErrorOr (struct with list for errors):** Similar to OneOf for the success case (just a struct containing T and maybe a null list). For error case, allocates a list and error objects. The struct likely has a reference to a list, which is null on success, non-null on failure. So memory usage is two pointers (one for the reference to list, one inside that list object to the array) plus the error objects. If only one error, it might optimize by not using a full `List<Error>` (unclear without digging into implementation). But generally, it’s one allocation for the list + one per error. Speed of checking success or error is just checking a flag or if list is null. The fluent methods add overhead mostly in terms of more function calls (which likely get inlined) and lambdas for the chaining. But this is the cost of clarity – not too high and usually worth it.

- **FluentResults (class-based):** Allocation of a Result object every time. Also allocation of Error objects for each error, and possibly small Success objects if you use success messages. The Result contains collections (probably `List<IError>`). It might initialize lazily or always have an empty list. Accessing `IsFailed` is just a bool check internally. The additional features like storing multiple errors mean there’s a bit more overhead to add errors (list manipulations). If you’re doing thousands of results per second, this will have more GC impact than OneOf or ErrorOr, but still far less than using exceptions for control flow. In exchange, you get a lot of convenience in managing those errors.

- **LanguageExt Either/Option (structs):** Similar to OneOf in efficiency. Option is basically a bool + value. Either is like OneOf< L,R > under the hood. Their implementations are highly optimized (e.g., using [StructLayout] to put the two values in overlapping slots in some versions). They also often avoid heap allocation by using structs to represent none or left (for instance, `Option<SomeClass>` when None might avoid allocating an Option object – it’s just a default struct with a flag false). Matching is efficient with provided methods. If you use the LINQ query syntax, each `select` or `where` gets translated into calls to library methods (like `.Bind`), which might use delegates but typically static ones.

- **LanguageExt [Union] codegen (class-based cases):** Similar to manual class hierarchy: each union value is an object of a generated class. Slight overhead per value allocated. The generated `Match` likely uses pattern matching or type checking internally but since it knows all cases, it can do if-else or a type switch efficiently. It could also use double-dispatch via an interface method on the cases (some codegens do that: each case class might implement an accept method that calls the appropriate function). Either way, it’s not going to be slow. The overhead is the same story of an extra object header vs. a big struct.

In practice, the performance differences **between these approaches are small for most applications**. The biggest win is avoiding exceptions for normal logic. All these libraries do that, so they all confer the huge benefit of not relying on try/catch for expected outcomes (with OneOf and ErrorOr showing dramatic improvements in throughput in microbenchmarks compared to exceptions ([Matthew Regis - Discriminated Unions With OneOf](https://matthewregis.dev/posts/discriminated-unions-with-one-of#:~:text=Method%20Mean%20Error%20StdDev%20Min,14%20KB))). Between each other, OneOf might use a tad less memory than FluentResults in a scenario with lots of single-case successes, for example, but unless you profile and find this to be a bottleneck, the choice should be based more on how well the library fits your needs and coding style.

## Choosing an Approach: Union Types vs Tuples (and Beyond)

To decide between these patterns, consider **expressiveness** and **safety** versus **overhead**. Tuple-based approaches have the least overhead but also the least safety – they rely on convention. If you return `(bool, T, string)` as a makeshift union, any developer using it must remember the protocol (“check the bool, if false the string is the error”). It’s easy to make a mistake. A discriminated union type (whether from a library or future C#) bakes that protocol into the type system – you literally can’t access the wrong thing without jumping through hoops. That safety often pays for itself in preventing bugs that are hard to catch (like forgetting a check in some code path).

Moreover, union types (class or struct based) integrate with other language features like pattern matching and generics better than raw tuples. For example, you can write a method that returns `OneOf<A,B>` and another that returns `OneOf<A,B,C>` and overload on them, whereas overloading on different tuple arities is less clear. Union types can implement interfaces or be passed polymorphically (e.g., you could make `Result<T>` implement some interface `IResult` if needed), giving more flexibility in large designs.

**Exhaustiveness and maintainability** is a key strength of union patterns. If a new case is added, union types prompt you to handle it (OneOf via compile errors in Match, FluentResults/ErrorOr via perhaps new branch in code, though not as enforced). With tuples, adding a new case means changing a tuple shape (from, say, bool to an enum with more values and adding a field), and that doesn’t automatically break consumers – it might just lead to incorrect logic if someone doesn’t update a switch on that enum. As one discussion in the C# design notes pointed out, having a *closed set of union cases* is more robust for evolving code ([GitHub - mcintyre321/OneOf: Easy to use F#-like ~discriminated~ unions for C# with exhaustive compile time matching](https://github.com/mcintyre321/OneOf#:~:text=,be%20handled)).

When considering performance, remember that in high-level application code, the difference between allocating one small object vs zero allocations is usually negligible compared to I/O or complex calculations. It only matters in tight loops or infrastructure code. The union libraries here are all designed to minimize overhead as much as possible without sacrificing the API. They’re generally all *fast enough* for virtually any application-level use. If you’re writing something extremely performance-sensitive (say a game engine tick loop), you might lean toward struct-based unions (OneOf or LanguageExt’s structs) or even manual struct to avoid any allocations. But even then, clarity might trump a micro-optimization.

**Interoperability** can also influence the choice. For example, if you need to return results from a web API, a library like FluentResults or ErrorOr might plug into a nice convention to produce standardized error responses (e.g., converting to HTTP Problem Details). Indeed, FluentResults has an ASP.NET Core integration package ([GitHub - altmann/FluentResults: A generalised Result object implementation for .NET/C#](https://github.com/altmann/FluentResults#:~:text=,it%2C%20test%20it%2C%20%2075)), and ErrorOr is often used with minimal APIs to map errors to responses. OneOf is more primitive in that sense – you’d have to write the mapping logic yourself (which is not hard, just a switch on the type). LanguageExt’s types can integrate with its own ecosystem (like use `Option` in place of nullable references and have consistency throughout).

Finally, consider **team familiarity and project style**. If your team is used to object-oriented code and just wants a clearer way to return errors, something like FluentResults/ErrorOr is easy to grasp (it feels like working with a slightly special object). If your team has functional programming experience or wants to adopt it, OneOf or LanguageExt’s Either/Option will fit nicely. If you need multiple different union types and want them strongly typed, you might choose OneOf for quick cases or LanguageExt’s codegen for more complex ones.

**Tuples vs union classes vs union structs**: A quick comparison:
- *Tuples:* Best for quick throwaway grouping of data, not for long-term APIs. Low safety, low overhead.
- *Manual Union Classes:* Clear and tailored to your scenario, moderately safe (if sealed and used carefully), moderate overhead (1 alloc per value).
- *OneOf/Either (Union Structs):* Very safe (exhaustive matching enforced), very low overhead (no alloc for container), good generality but requires using their patterns (which is easy).
- *Result/Option Libraries (ErrorOr, FluentResults, etc.):* Safe (forces you to handle success/fail distinctly), some come with additional overhead for rich info. They provide a lot of convenience if error reporting is needed.
- *LanguageExt [Union] codegen:* Safe and clear (auto exhaustive match via generated code), overhead similar to manual classes. Best if you have many cases or want auto-generated equality, etc.

To highlight limitations: tuple approaches fall apart if the number of possibilities grows or if the cases are unrelated types. You don’t want a method returning `(Customer?, Supplier?)` – that’s ambiguous (did it return a Customer or a Supplier? you need an extra flag anyway). That’s exactly a discriminated union scenario begging for a union type (e.g., `OneOf<Customer, Supplier>`). Also, debugging and logging are easier with union types: you can `ToString()` a OneOf or a Result and get something like "NameTaken" or "Error: Name is too short", whereas a tuple would just be like `(false, null, "Name too short")` which is less descriptive unless you know the convention.

In the end, discriminated unions (in whatever form) bring an **intentional design** to your code: you are explicitly modeling that “X or Y can happen,” and that intent is encoded in the type system. This makes code more self-documenting and often cleaner, at the cost of introducing a new type abstraction. As C# continues to evolve, we may see native support that makes this even more seamless ([csharplang/proposals/TypeUnions.md at main · dotnet/csharplang · GitHub](https://github.com/dotnet/csharplang/blob/main/proposals/TypeUnions.md#:~:text=Many%20other%20languages%20already%20do,one%20or%20more%20limited%20forms)). But even now, with the tools above, C# developers can enjoy much of the same benefits. Each approach has pros and cons, but they all aim to make code more **robust, readable, and correct** by leveraging the type system instead of implicit conventions or exceptions.

# Conclusion

Discriminated unions fulfill a fundamental need in programming: the ability to represent a value that can be one of a few predefined forms, in a way that the compiler *knows* about all the forms. This leads to safer and often clearer code. In 2025, C# is on the path toward introducing union types natively, potentially in stages (union classes, union structs, etc.) ([Discriminated Unions in C# update : r/dotnet](https://www.reddit.com/r/dotnet/comments/1i2q3kx/discriminated_unions_in_c_update/#:~:text=Looks%20like%20DUs%20might%20be,team%20had%20the%20other%20day)) ([Discriminated Unions in C# design update (Jan 2025)](https://davecallan.com/discriminated-unions-csharp-update/#:~:text=Looks%20like%20progress%20anyhow%2C%20and,in%20one%20version%20of%20C)). Until then, the community solutions have you covered. 

For intermediate C# developers, learning to use these patterns and libraries can significantly improve how you handle multiple outcome scenarios. Rather than defaulting to exceptions or nulls, you can make the possibilities explicit. For example, instead of writing documentation “// returns null if not found”, you can return `Option<T>` or `OneOf<T, NotFound>` – and the code itself tells the story. Instead of throwing an exception to indicate a validation error that someone might catch far away, you can return an `ErrorOr<ValidatedData>` that forces the caller to deal with the invalid state right there.

Each library we discussed brings its flavor:
- **Use OneOf** when you want a quick, generic union of a few types with exhaustive handling – it’s minimal and effective ([GitHub - mcintyre321/OneOf: Easy to use F#-like ~discriminated~ unions for C# with exhaustive compile time matching](https://github.com/mcintyre321/OneOf#:~:text=)) ([GitHub - mcintyre321/OneOf: Easy to use F#-like ~discriminated~ unions for C# with exhaustive compile time matching](https://github.com/mcintyre321/OneOf#:~:text=,returning%20custom%20Typed%20error%20objects)).
- **Use ErrorOr** when you specifically want to model operations that can fail, and you like fluent chaining but still want a lightweight result object (especially in web apps for error handling).
- **Use FluentResults** when you need to accumulate multiple errors or attach rich metadata to failures, and you prefer a fluent, customizable approach to results (common in enterprise apps where error details matter).
- **Use LanguageExt** when you are embracing functional patterns broadly – its union types (Either/Option) and codegen can unify error handling, optionals, and custom unions under one paradigm, with lots of supporting tools.

And if none of these fit exactly, you can always create your own discriminated union with a few classes or records. It’s a bit more work, but as we showed, it’s quite doable and yields the benefits of clarity and closed sets (just remember the limitations regarding exhaustiveness).

To circle back to the need: C# is a powerful language, but without union types, developers historically resorted to less optimal patterns (flags, base classes with unused members, etc.) ([csharplang/proposals/TypeUnions.md at main · dotnet/csharplang · GitHub](https://github.com/dotnet/csharplang/blob/main/proposals/TypeUnions.md#:~:text=You%20might%20think%20you%20can,let%20it%20be%20just%20anything)) ([csharplang/proposals/TypeUnions.md at main · dotnet/csharplang · GitHub](https://github.com/dotnet/csharplang/blob/main/proposals/TypeUnions.md#:~:text=Another%20is%20the%20inability%20to,from%20within%20the%20same%20hierarchy)). Embracing discriminated unions – whether through a library or future language support – leads to more **intentional API design**. Consumers of your code immediately know “I must handle these cases,” and you as the producer of a result have a clear way to represent every meaningful outcome. It’s a win-win for correctness and maintainability. Given the ongoing work by the .NET team, it’s safe to say that discriminated unions are recognized as an important feature whose time in C# is coming ([csharplang/proposals/TypeUnions.md at main · dotnet/csharplang · GitHub](https://github.com/dotnet/csharplang/blob/main/proposals/TypeUnions.md#:~:text=Many%20other%20languages%20already%20do,one%20or%20more%20limited%20forms)). Until then, the libraries above are excellent choices to elevate your code today. Each carries certain opinions and trade-offs (hence our evaluative look at them), but all of them will help you write code that is more explicit and robust in the face of multiple possible types or results – and that’s a hallmark of clean, reliable software design.

