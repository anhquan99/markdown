# Lambda expressions
 - Used to create anonymous function.
 - To create a lambda expression, use `=>` to separate the lambda parameter list from its body.
   `(input-parameters) => {<sequence of statements>}`
- 2 types of expressions:
	- **Statement lambdas:** resembles an expression lambda except that its statements are enclosed in braces
	  `(input-parameters) => {<sequence of statements>}`
	- **Expression lambdas:** an expression on the right side of the `=>` operator is called an expression lambda, it returns a result
	  `(input parameters) => expression`
- Lambda expression can be converted to a delegate type. If a lambda expression doesn't return a value, it can be converted to one of the `Action` delegate types, otherwise it can be converted to one of the `Func` delegate types.
- We can use async in lambda.
- A variable that is captured won't be garbage-collected until the delegate that references it becomes eligible for garbage collection
- Variables introduced within a lambda expression aren't visible in the enclosing method
- A lambda expression can't directly capture an `in`, `ref`, `out` parameter from the enclosing method.
- A return statement in a lambda doesn't cause the enclosing method to return
## Explicit return type
- In C# 10 you can specify the return type of lambda expression before the input parameters. When you specify an explicit return type, you must parenthesize the input parameters
  `var choose = object (bool b) => b ? 1 : "two"; // Func<bool, object>`
## Input parameters of a lambda expression
- You enclose input parameters of a lambda expression in parentheses or none parentheses
  ``````csharp
  Action line = () => Console.Writeline();
  Func<double, double> cube = x => x*x*x;
  ``````
- In C# 9 you can use discards to specify two or more input parameters of a lambda expression that aren't used in the expression
  `Fun<int, int, int> constant = (_, _) => 42;`
## Extension methods
- Extension methods enable you to "add" methods to existing types without creating a new derived type, recompiling, or otherwise modifying the original type.
- Binding extension methods at compile time, you can use extension methods to extend a class or interface, but not to override them. An extension method with the same name and signature as an interface or class method will never be called.  At compile time, extension methods always have lower priority than instance methods defined in the type itself.
- **Common usage pattern:**
	- Layer-specific functionality: When using an Onion Architecture or other layered application design, it's common to have a set of Domain Entities or Data Transfer Objects that can be used to communicate across application boundaries. These objects contain no functionality. Extension method can be used to add functionality that is specific to each application layer without loading object down with methods not needed or wanted in other layers.
	- Extending predefined types: Rather than creating a new object when reusable functionality needs to be created, we can often extend an exsting type. As an example, if we don't use extension methods, we might create an `Engine` or `Query` class to do the work of executing a query on a SQL Server that may be called from multiple places in our code. However, we can instead extend the `System.Data.SqlClient.SqlConnection` class using methods to perform that query from anywhere we have a connection to a SQL Server.

