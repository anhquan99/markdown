# Common Coding Conventions
## General Language Guidelines
- **Data Types:** Use C# language keywords (e.g., `string`, `int`) instead of .NET runtime types (`System.String`, `System.Int32`).
- **Numbers:** Prefer `int` over unsigned types unless interacting with specific APIs that require them.
- **Exceptions:** Catch specific exceptions rather than general ones (like `System.Exception`).
- **Async/Await:** Use `async` and `await` for I/O-bound operations and use `Task.ConfigureAwait` to avoid deadlocks.
- **Delegates:** Use built-in `Func<>` and `Action<>` instead of defining custom delegate types.
- **Static Members:** Always call static members using their defining class name, never a derived class name.
## Variable Declarations (`var`)
- **When to use `var`:** Use it when the type is explicitly obvious from the right side of the assignment (e.g., when using `new`, an explicit cast, or a literal).
- **When NOT to use `var`:** Do not use it if the type is ambiguous or hidden behind a method call. Do not rely on variable names to imply the type.
- **Loops:** Use `var` for standard `for` loops, but use **explicit typing** for `foreach` loops since the collection's inner type isn't always obvious.
## Strings and Collections
### Strings
- Use **string interpolation** (`$"{variable}"`) to concatenate short strings.
- Prefer **raw string literals** (`"""`) over escaped strings or verbatim strings for multi-line text.
- Use `System.Text.StringBuilder` when appending strings inside a loop.
### Collections
- Use modern **collection expressions** (e.g., `string[] vowels = ["a", "e", "i"];`) to initialize arrays and collections.
## Initialization and Constructors
- **Primary Constructors:** Use **PascalCase** for record parameters, and **camelCase** for class/struct parameters.
- **Required Properties:** Prefer using the `required` modifier and `init` accessors to force initialization, rather than writing bloated constructors.
- **Instantiation:** Use the concise `new()` syntax when the type is known, and utilize object initializers (`new Example { Name = "Test" }`) instead of assigning properties line-by-line.
## Control Flow and Error Handling
- **Logical Operators:** Always use short-circuiting operators (`&&`, `||`) rather than standard bitwise ones (`&`, `|`) in comparisons to prevent unnecessary evaluations and runtime errors.
- **Disposal:** Prefer the modern, brace-less `using` declaration (e.g., `using Font font = new Font();`) over wrapping code in `try-finally` or using bracketed `using` blocks.
- **Events:** Use lambda expressions for simple event handlers that do not need to be unregistered later.
## LINQ Queries
- **Variables:** Use implicit typing (`var`) for query results, especially since they often return anonymous or nested generic types.
- **Structure:** Align query clauses underneath the `from` clause.
- **Filtering:** Place `where` clauses as early as possible so subsequent operations run on a reduced dataset.
- **Joins:** Access inner collections by chaining multiple `from` clauses rather than using a `join` clause.
## Layout and Formatting Conventions
- **Namespaces:** Use **file-scoped namespaces** (`namespace MySampleCode;`).
- **Usings:** Place `using` directives **outside** the namespace declaration to avoid naming collisions and resolution confusion.
- **Braces:** Use the "Allman" style (open and closing braces go on their own separate lines and align with the current indentation).
- **Spacing:** Use **4 spaces** for indentation (never tabs). Limit lines to 65 characters where possible, and only write one statement/declaration per line.
- **Comments:** Use `//` for single-line comments (with a space after the slashes) and begin with a capital letter. Avoid `/* */` for multi-line explanations. Use XML comments (`///`) for public APIs.
## References
- [Common C# code conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions?source=recommendations)