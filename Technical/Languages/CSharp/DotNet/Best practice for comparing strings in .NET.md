Reference https://docs.microsoft.com/en-us/dotnet/core/extensions/globalization-and-localization
## Recommendations for string usage
- Use overloads that explicitly specify the string comparison rules for string operations.
  Ex: string.Equals();
- Use `StringComparison.Ordinal` or `StringComparison.OrdinalIgnoreCase` for comparison as your safe default for culture-agnostic string matching, and for better performance.
- Use string operations that are based on `StringComparison.CurrentCulture` when you display output to the user.
- Use the non-linguistic [StringComparison.Ordinal](https://docs.microsoft.com/en-us/dotnet/api/system.stringcomparison#system-stringcomparison-ordinal) or [StringComparison.OrdinalIgnoreCase](https://docs.microsoft.com/en-us/dotnet/api/system.stringcomparison#system-stringcomparison-ordinalignorecase) values instead of string operations based on [CultureInfo.InvariantCulture](https://docs.microsoft.com/en-us/dotnet/api/system.globalization.cultureinfo.invariantculture) when the comparison is linguistically irrelevant (symbolic, for example).
- Use the [String.ToUpperInvariant](https://docs.microsoft.com/en-us/dotnet/api/system.string.toupperinvariant) method instead of the [String.ToLowerInvariant](https://docs.microsoft.com/en-us/dotnet/api/system.string.tolowerinvariant) method when you normalize strings for comparison.
-   Use an overload of the [String.Equals](https://docs.microsoft.com/en-us/dotnet/api/system.string.equals) method to test whether two strings are equal.
-   Use the [String.Compare](https://docs.microsoft.com/en-us/dotnet/api/system.string.compare) and [String.CompareTo](https://docs.microsoft.com/en-us/dotnet/api/system.string.compareto) methods to sort strings, not to check for equality.
-   Use culture-sensitive formatting to display non-string data, such as numbers and dates, in a user interface. Use formatting with the [invariant culture](https://docs.microsoft.com/en-us/dotnet/api/system.globalization.cultureinfo.invariantculture#system-globalization-cultureinfo-invariantculture) to persist non-string data in string form.
## Avoid when comparing strings
-   Do not use overloads that do not explicitly or implicitly specify the string comparison rules for string operations.
-   Do not use string operations based on [StringComparison.InvariantCulture](https://docs.microsoft.com/en-us/dotnet/api/system.stringcomparison#system-stringcomparison-invariantculture) in most cases. One of the few exceptions is when you are persisting linguistically meaningful but culturally agnostic data.
-   Do not use an overload of the [String.Compare](https://docs.microsoft.com/en-us/dotnet/api/system.string.compare) or [CompareTo](https://docs.microsoft.com/en-us/dotnet/api/system.string.compareto) method and test for a return value of zero to determine whether two strings are equal.
## Select an overload that does not use default values
- Some overloads with default parameters (char search) perform an ordinal comparison, while other (string search) are culture-sensitive
- Know the intent of the code
  ``````csharp
	// bad usage
	string protocol = GetProtocol(url);
	if (String.Equals(protocol, "http")) {
	   // ...Code to handle HTTP protocol.
	}
	else {
	   throw new InvalidOperationException();
	}
  ``````

  ``````csharp
	// good usage
		string protocol = GetProtocol(url);
		if (String.Equals(protocol, "http", StringComparison.OrdinalIgnoreCase)) {
		   // ...Code to handle HTTP protocol.
		}
		else {
		   throw new InvalidOperationException();
		}
  ``````
![[Pasted image 20220325161333.png]]
![[Pasted image 20220325161612.png]]
## Choosing a StringComparison member for your method call
Data | Behavior | Corresponding System.StringComparison value
------------ | ------------ | ------------ 
Case-sensitive internal identifiers.<br/>Case-sensitive identifiers in standards such as XML and HTTP.<br/>Case-sensitive security-related settings. | A non-linguistic identifier, where bytes match exactly. | Ordinal
Case-insensitive internal identifiers. <br/>Case-insensitive identifiers in standards such as XML and HTTP.<br/> File paths. <br/>Registry keys and values. <br/>Environment variables. <br/>Resource identifiers (for example, handle names). <br/>Case-insensitive security-related settings. | A non-linguistic identifier, where case is irrelevant; especially data stored in most Windows system services. | OrdinalIgnoreCase
Some persisted, linguistically relevant data. <br/>Display of linguistic data that requires a fixed sort order. | Culturally agnostic data that still is linguistically relevant. | InvariantCulture or InvariantCultureIgnoreCase
Data displayed to the user.  <br/>Most user input. | Data that requires local linguistic customs. | CultureCurrent or CurrentCultureIgnoreCase

## Common string comparison methods in .NET
- `String.Compare`
	- Default interpretation: `StringComparison.CurrentCulture`
- `String.CompareTo`
	- Default interpretation: `StringComparison.CurrentCulture`
	- This method currently does not offer an overload that specifies a `StringComparison` type.
- `String.Equals`
	- Default interpretation: `StringComparison.Ordinal`
- `String.ToUpper` and `String.ToLower`
	- Default interpretation: `StringComparison.CurrentCulture`
	- Be careful when you use these methods, because forcing a string to uppercase and lowercase if often used as a small normalization for comparing strings regardless of case. If so, consider using a case-insensitive comparison.
	- The `String.ToUpperInvariant` and the `String.ToLowerInvariant` methods are also available. `ToUpperInvariant` is the standard way to normalize case. Comparisons made using `StringComparison.OrdinalIgnoreCase` are behaviorally the composition of two calls: calling `ToUpperInvariant` on both string arguments, and doing a comparison using `StringComparison.Ordinal`.
- `Char.ToUpper` and `Char.ToLower`
	- Default interpretation: `StringComparison.CurrentCulture`
- `String.IndexOf` and `String.LastIndexOf`
	- Default interpretation: `StringComparison.CurrentCulture`
- `Array.Sort` and `Array.BinarySearch`
	- Default interpretation: `StringComparison.CurrentCulture`
