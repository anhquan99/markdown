# Lambda expression
 - Used to create anonymous function.
 - To create a lambda expression, use `=>` to separate the lambda parameter list from its body.
   `(input-parameters) => {<sequence of statements>}`
- 2 types of expressions:
	- **Statement lambdas:** resembles an expresion lambda expcept that its statements are enclosed in braces
	  `(input-parameters) => {<sequence of statements>}`
	- **Expressionj lambdas:** an expression on the right side of the `=>` operator is called an epxression lambda, it returns a result
	  `(input parameters) => expression`
- Lambda epxression can be converted to a delegate type. If a lambda epxression doesn't return a value, it can be converted to one of the `Action` delegate types, otherwise it can be converted to one og the `Func` delegate types.
- We can use async in lambda
- A variable that is captured won't be garbage-collected until the delegate that references it becomes eligible for garbage collection
- Variables introduced within a lambda epxression aren't visible in the enclosing method
- A lambda epxreesion can't directly capture an `in`, `ref`, `out` parameter from the enclosing method.
- A return statement in a lambda doesn't cause the enclosing method to return
## Explicit return type
- In C# 10 you can specify the return type of lambda expression before the input parameters. When you specify an explicit return type, you must parenthesize the input parameters
  `var choose = object (bool b) => b ? 1 : "two"; // Func<bool, object>`
## Input parameters of a lambda expression
- You enclose input parameters of a lambda expression in parenthese or none parenthese
  ``````c#
  Action line = () => Console.Writeline();
  Func<double, double> cube = x => x*x*x;
  ``````
- In C# 9 you can use discards to specify two or more input parameters of a lambda expression that aren't used in the expression
  `Fun<int, int, int> constant = (_, _) => 42;`

