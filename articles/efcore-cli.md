**Common mistakes with EF Core CLI tool**

- [No C# files created](#no-c-files-created)
  - [Check for Output Directory and Namespace Parameters](#check-for-output-directory-and-namespace-parameters)
  - [Are You Using a Database with No Tables?](#are-you-using-a-database-with-no-tables)
  - [No Permissions / Wrong Connection String](#no-permissions--wrong-connection-string)
  - [You Filtered Out All Tables](#you-filtered-out-all-tables)
  - [Database Incompatibility or Unsupported Objects](#database-incompatibility-or-unsupported-objects)
  - [EF Tools Installed Correctly?](#ef-tools-installed-correctly)
  - [Final Recommendation](#final-recommendation)


# No C# files created

If `dotnet ef dbcontext scaffold` runs without errors but doesn't generate any C# class files, it's likely due to one of the following reasons. Here's a checklist to help you troubleshoot:

---

## Check for Output Directory and Namespace Parameters

You may have run the command without specifying an output directory (`-o`) or the generated files may have gone to an unexpected location.

What to Check:

* Look in your current working directory and any subdirectories.
* Try adding explicit output parameters:

```bash
dotnet ef dbcontext scaffold "your-connection-string" Microsoft.EntityFrameworkCore.SqlServer -o Models -f
```

This generates the files in a `Models` folder and overwrites existing ones (`-f` is optional but helpful during re-scaffolding).

## Are You Using a Database with No Tables?

If your database has no user tables, EF Core will silently succeed and generate nothing. This is surprisingly common with newly created databases.

How to Confirm:

Run this SQL against your database:

```sql
SELECT name FROM sys.tables WHERE is_ms_shipped = 0;
```

If the result is empty, that's your issue.

## No Permissions / Wrong Connection String

You might be connecting to a valid database but not the one you think. If your connection string is wrong or pointing to a different server or database, you might be scaffolding from a database that has no schema.

What to Check:

* Verify the connection string carefully.
* Add `-v` (verbose) to the command to see detailed output and check what database it connects to:

```bash
dotnet ef dbcontext scaffold "your-connection-string" Microsoft.EntityFrameworkCore.SqlServer -v
```

## You Filtered Out All Tables

If you used `--table` or `--schema` filters incorrectly, EF might exclude everything.

Example:

```bash
dotnet ef dbcontext scaffold "your-connection-string" Microsoft.EntityFrameworkCore.SqlServer --schema Sales
```

This only includes tables in the `Sales` schema. If that schema has no tables, you'll get nothing.

## Database Incompatibility or Unsupported Objects

EF Core doesn’t scaffold:

* Views (unless you use EF Core Power Tools or tweak manually)
* Stored procedures
* Synonyms
* System tables

So if your database has only those kinds of objects, again, no output is generated.

## EF Tools Installed Correctly?

If you're using `dotnet-ef`, make sure you're using the same versions across your project and globally.

What to Run:

```bash
dotnet tool list -g
dotnet ef --version
```

Also check the project has appropriate EF Core NuGet packages:

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="..." />
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="..." />
```

## Final Recommendation

Try running this command:

```bash
dotnet ef dbcontext scaffold "your-connection-string" Microsoft.EntityFrameworkCore.SqlServer -o Models -f -v
```

* `-o Models`: Writes all files to a `Models` folder
* `-f`: Overwrites if files exist
* `-v`: Shows detailed output to debug further
