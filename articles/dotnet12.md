**My .NET 11 edition book's support for .NET 12**

- [.NET 12 downloads and announcements](#net-12-downloads-and-announcements)
- [How to switch from .NET 11 to .NET 12](#how-to-switch-from-net-11-to-net-12)
  - [Upgrading the target framework for a project](#upgrading-the-target-framework-for-a-project)
  - [Upgrading packages for a project](#upgrading-packages-for-a-project)
  - [Download CPM files for my .NET 10 and .NET 11 books](#download-cpm-files-for-my-net-10-and-net-11-books)
  - [Preview package version numbers](#preview-package-version-numbers)
- [.NET 13 downloads and announcements](#net-13-downloads-and-announcements)

# .NET 12 downloads and announcements

Microsoft will release previews of .NET 12 regularly starting on Tuesday, February 9, 2027 until the general availability (GA) version on Tuesday, November 11, 2027.

- [Download .NET 12.0 SDK](https://dotnet.microsoft.com/download/dotnet/12.0)
- [.NET 12 Release Notes](https://github.com/dotnet/core/blob/main/release-notes/12.0/README.md)
- [February 2026: .NET 12 Preview 1](https://github.com/dotnet/core/blob/main/release-notes/12.0/preview/preview1/README.md)
- March 2026: .NET 12 Preview 2
- April 2026: .NET 12 Preview 3
- May 2026: .NET 12 Preview 4
- June 2026: .NET 12 Preview 5
- July 2026: .NET 12 Preview 6
- August 2026: .NET 12 Preview 7
- September 2026: .NET 12 Release Candidate 1
- October 2026: .NET 12 Release Candidate 2
- November 2026: .NET 12.0 GA (general availability)

# How to switch from .NET 11 to .NET 12

After [downloading](https://dotnet.microsoft.com/download/dotnet/12.0) and installing .NET 12.0 SDK, follow the step-by-step instructions in the book and they should work as expected since the project file will automatically reference .NET 12.0 as the target framework. 

## Upgrading the target framework for a project

To upgrade a project in the GitHub repository from .NET 10.0 or 11.0 to .NET 12.0 often only requires a target framework change in your project file.

Change this:

```xml
<TargetFramework>net10.0</TargetFramework>
```

Or this:

```xml
<TargetFramework>net11.0</TargetFramework>
```

To this:

```xml
<TargetFramework>net12.0</TargetFramework>
```

> **Warning!** Sometimes it is better to recreate a project because the project template and APIs may have changed and only changing the target framework is not enough. This is especially true for third-party project templates like Umbraco CMS.

## Upgrading packages for a project

For projects that reference additional NuGet packages, use the latest NuGet package version instead of the version given in the book. For example, you might reference a package, as shown in the following markup:
```xml
<ItemGroup>
  <PackageReference
    Include="Microsoft.Extensions.Configuration.Binder"
    Version="11.0.0" />
</ItemGroup>
```

To use .NET 11 Preview 1 packages, search https://www.nuget.org for the package and find its latest preview version number. For example, for Preview 1, as shown in the following markup:
```xml
<ItemGroup>
  <PackageReference
    Include="Microsoft.Extensions.Configuration.Binder"
    Version="12.0.0-preview.1.x.x" />
</ItemGroup>
```

> You can search for the correct NuGet package version numbers yourself at the following link: https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder#versions-body-tab.

To always use latest .NET 12 preview, release candidate, or patch version package, use a version number wildcard (not supported if you are using CPM), as shown in the following markup:
```xml
<ItemGroup>
  <PackageReference
    Include="Microsoft.Extensions.Configuration.Binder"
    Version="12.0-*" />
</ItemGroup>
```

## Download CPM files for my .NET 10 and .NET 11 books

C# 15 and .NET 11 - Modern Cross-Platform Development Fundamentals
https://github.com/markjprice/cs15net11/blob/main/code/ModernNet/Directory.Packages.props

C# 14 and .NET 10 - Modern Cross-Platform Development Fundamentals
https://github.com/markjprice/cs14net10/blob/main/code/ModernWeb/Directory.Packages.props

Apps and Services with .NET 10
https://github.com/markjprice/apps-services-net10/blob/main/code/Directory.Packages.props

Real-World Web Development with .NET 10
https://github.com/markjprice/web-dev-net10/blob/main/code/MatureWeb/Directory.Packages.props

Tools and Skills for .NET 10
https://github.com/markjprice/tools-skills-net10/blob/main/code/Directory.Packages.props

## Preview package version numbers

- .NET 12 Preview 1: `12.0.0-preview.1.x.x`
- .NET 12 Preview 2: `12.0.0-preview.2.x.x`
- .NET 12 Preview 3: `12.0.0-preview.3.x.x`
- .NET 12 Preview 4: `12.0.0-preview.4.x.x`
- .NET 12 Preview 5: `12.0.0-preview.5.x.x`
- .NET 12 Preview 6: `12.0.0-preview.6.x.x`
- .NET 12 Preview 7: `12.0.0-preview.7.x.x`
- .NET 12 Release Candidate 1: `12.0.0-rc.1.x.x`
- .NET 12 Release Candidate 2: `12.0.0-rc.2.x.x`
- .NET 12.0 GA (general availability): `12.0.0`

# .NET 13 downloads and announcements

Microsoft will release previews of .NET 13 regularly starting in February 2028 until the final version on Tuesday, November 10, 2028.

- [Download .NET 13.0 SDK](https://dotnet.microsoft.com/download/dotnet/13.0). **Warning!** This link will not activate until February 2028.
