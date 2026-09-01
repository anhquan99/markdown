# Naming Convention
## Mandatory Naming Rules (Compiler Enforced)
- An identifier is the name assigned to a type, member, variable, or namespace. To be valid and compile successfully, identifiers must adhere to these rules:
	- **Starting Character:** Must begin with a letter or an underscore (`_`).
	- **Allowed Characters:** Can include Unicode letters, decimal digits, connecting characters, combining characters, and formatting characters.
	- **Reserved Keywords:** To use a C# keyword as a name (e.g., `if`), you must prefix it with an `@` symbol (e.g., `@if`).
## General Casing Conventions
- **PascalCase** (e.g., `DataService`, `StartEventProcessing`)
    - Classes, structs, interfaces, delegates, enums, and records.
    - Namespaces.
    - All `public` members (fields, properties, events, methods, and local functions).
    - All constant names (including private and internal).
- **camelCase** (e.g., `workerQueue`, `isValid`)
    - Method arguments and local variables.
    - `private` or `internal` non-constant fields.
## Specific Naming Patterns & Prefixes
- **Interfaces:** Must start with a capital `I` (e.g., `IWorkerQueue`).
- **Attributes:** Must end with the word `Attribute`.
- **Enums:** Use singular nouns for non-flag enums, and plural nouns for flag enums.
- **Private/Internal Fields:** Start with an underscore `_` followed by camelCase (e.g., `_workerQueue`).
- **Static Fields:** Start with `s_` (e.g., `s_workerQueue`).
- **Thread-Static Fields:** Start with `t_` (e.g., `t_timeSpan`).
- **Generic Type Parameters:** Prefix descriptive names with `T` (e.g., `TSession`). If it is a single-letter placeholder, just use `T`.
## Primary Constructor Parameters
- **Classes and Structs:** Use **camelCase**, as they behave like standard method parameters.
- **Records:** Use **PascalCase**, as the parameters automatically become public properties of the record.
## Best Practices
- Prefer clarity over brevity; use meaningful and descriptive names.
- Avoid abbreviations and acronyms unless they are widely accepted.
- Do not use two consecutive underscores (`__`), as these are reserved for compiler-generated identifiers.
- Avoid single-letter names, except for simple loop counters or standard syntax examples.
## References
- https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/identifier-names