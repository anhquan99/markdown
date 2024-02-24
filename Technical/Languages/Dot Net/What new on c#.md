# Local function
- Delare in function
- Function in function
- Nearest scope piority
- Scope chaining variable like JS
- Can be static 
# Out variable declaration
- Delaration in the parameter
# Nullable value type
- Allow variable reference type null
- We can check null value by using operator `?`
  ``````c#
  a?.property;
  a?.property1?.property2 ?? default value;
  ``````
# [null-forgiving) operator - C# reference | Microsoft Docs](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-forgiving]
# Switch case when 
# Pattern matching
# Expression body function
# Using Declaration
- When finish scope it will be dispose
# Indices and ranges
- `^1` it will access with position length - 1
- `arr[start..end]` take 2 element `arr[start]` and `arr[end]`
- Take all `arr[..]`
- `Range` datatype
# Tuples
- Deconstructor is used when convert reference type into tuples
# Default interface
- Interface
	- Contract more than abstract
- Define function in interface, if an object from a class implement from a interface an not implementing defined function, it will not throw an error.
# init only for property
``````c#
public class user{
	public string Username {get; init;} // username can be set when initialize in constructor
	public user(string username){
		Username = username;
	}
}
``````
# Target type expression
- Return type when return type has been targeted
- Not apply when using var
# Record type
- `with` will automactically clone object
# Asynchonous