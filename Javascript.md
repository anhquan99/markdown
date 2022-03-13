# Primitive type
- String
- Number
- BigInt
- Boolean
- Undefined
- Null
- Symbol
# Hoisting
- Is a default behavior of js where all variable and function declarations are moved on top.
- This means where the variables and functions declared, they are moved on top of the scope or global.
# == vs ===
- == compare values
- === compare values and type
# Var vs let vs const
- ## Var:
	- If declare in a function it will have scope is in that function else globally scoped
	- Hoisting
- ## Let:
	- If declare in a block it will have block scope
	  ``````js
		let greeting = "say Hi";
		let times = 4;
		if (times > 3) {
			let hello = "say Hello instead";
			console.log(hello); // "say Hello instead"
		}
		console.log(hello); // hello is not defined

	  ``````
	- It can not reuse when scope is finish
	- Let can reassign but can not redefine
	- With different blocks using let for redefining will not get error
	- Hoisting but with undefined initialized value let will not have any initialized value. This make let can not use if not declare.
- ## Const:
	- block scope and hoisting
	- If variable is primitive it can not be redefined or assigned again
	- If variable is reference then variable can not be redefined or reassigned, but it can reassign its properties value.
# Type Coercion
- **String coercion:**
	- Take place when using `+` operator
- Number coercion:
	- 