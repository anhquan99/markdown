# Comparing string in .NET
## <span style="color:#00FF00">Recommendations for string usage</span>
- Use overloads that explicitly specify the string comparison rules for string operations.
  Ex: string.Equals();
- Use `StringComparison.Ordinal` or `StringComparison.OrdinalIgnoreCase` for comparison as your safe default for culture-agnostic string matching, and for better performance.
- Use string operations that are based on `StringComparison.CurrentCulture` when you display output to the user.
- Use the non-linguistic [StringComparison.Ordinal](https://docs.microsoft.com/en-us/dotnet/api/system.stringcomparison#system-stringcomparison-ordinal) or [StringComparison.OrdinalIgnoreCase](https://docs.microsoft.com/en-us/dotnet/api/system.stringcomparison#system-stringcomparison-ordinalignorecase) values instead of string operations based on [CultureInfo.InvariantCulture](https://docs.microsoft.com/en-us/dotnet/api/system.globalization.cultureinfo.invariantculture) when the comparison is linguistically irrelevant (symbolic, for example).
- Use the [String.ToUpperInvariant](https://docs.microsoft.com/en-us/dotnet/api/system.string.toupperinvariant) method instead of the [String.ToLowerInvariant](https://docs.microsoft.com/en-us/dotnet/api/system.string.tolowerinvariant) method when you normalize strings for comparison.
-   Use an overload of the [String.Equals](https://docs.microsoft.com/en-us/dotnet/api/system.string.equals) method to test whether two strings are equal.
-   Use the [String.Compare](https://docs.microsoft.com/en-us/dotnet/api/system.string.compare) and [String.CompareTo](https://docs.microsoft.com/en-us/dotnet/api/system.string.compareto) methods to sort strings, not to check for equality.
-   Use culture-sensitive formatting to display non-string data, such as numbers and dates, in a user interface. Use formatting with the [invariant culture](https://docs.microsoft.com/en-us/dotnet/api/system.globalization.cultureinfo.invariantculture#system-globalization-cultureinfo-invariantculture) to persist non-string data in string form.
## <span style="color:#00FF00">Avoid 
 when compare strings</span>
-   Do not use overloads that do not explicitly or implicitly specify the string comparison rules for string operations.
-   Do not use string operations based on [StringComparison.InvariantCulture](https://docs.microsoft.com/en-us/dotnet/api/system.stringcomparison#system-stringcomparison-invariantculture) in most cases. One of the few exceptions is when you are persisting linguistically meaningful but culturally agnostic data.
-   Do not use an overload of the [String.Compare](https://docs.microsoft.com/en-us/dotnet/api/system.string.compare) or [CompareTo](https://docs.microsoft.com/en-us/dotnet/api/system.string.compareto) method and test for a return value of zero to determine whether two strings are equal.
## <span style="color:#00FF00">Select an overload that does not use default values</span>
- Some overloads with default parameters (char search) perform an ordinal comparison, while other (string search) are culture-sensitive
- Know the intent of the code
  ``````C#
	// bad usage
	string protocol = GetProtocol(url);
	if (String.Equals(protocol, "http")) {
	   // ...Code to handle HTTP protocol.
	}
	else {
	   throw new InvalidOperationException();
	}
  ``````

  ``````C#
	// good usage
		string protocol = GetProtocol(url);
		if (String.Equals(protocol, "http", StringComparison.OrdinalIgnoreCase)) {
		   // ...Code to handle HTTP protocol.
		}
		else {
		   throw new InvalidOperationException();
		}
  ``````
- ![[Pasted image 20220325161333.png]]
- ![[Pasted image 20220325161612.png]]
