# Summary javascript
- Weakly Typed Language
	- No explicit type assignment
- Object oriented language 
	- Data can be organized in logical objects
	- Primitive and reference types
- Versatile language
	- Run in browser and directly on a PC/ server
	- Can perform a broad variety of tasks
# Primitive type
- String
	- Backticks: Are extended functionality quotes, they allow us to embed variables and expressions into a string by wrapping them in `&{...}`
	 ``````js
	let name = "John"; // embed a variable alert
	( `Hello, _${__name}_!` ); // Hello, John! // embed an expression
	alert( `the result is _${__1 + 2}_` ); // the result is 3
	``````
- Number
	- *Special numeric values:* `Infinity`, `NaN`
- BigInt
- Boolean
- Undefined: meaning the variable value is not assigned or not assigned
	- ![[Pasted image 20220330070133.png]]
- Null: means empty or not does not exist
- Symbol
# Hoisting
- Is a default behavior of JS where all variable and function declarations are moved on top.
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
- ## String coercion:
	- Take place when using `+` operator with string and number, number will be converted to string
- ## Number coercion
	- If we use `-` operator with string and number, string will be converted to number
	  ``````js
	  var x = 3;
	  Var y = "3";
	  x - y    //Returns 0 since the variable y (string type) is converted to a number type
	  ``````
- ## Boolean coercion
	- `0`, `-0`, `“”`, `null`, `undefined` and `NaN`
	 ``````js
	var x = 0;
	var y = 23;
		
	if(x) { console.log(x) }   // The code inside this block will not run since the value of x is 0(Falsy) 
		
	if(y) { console.log(y) }    // The code inside this block will run since the value of y is 23 (Truthy)
	 ``````
	- Logical operators in javascript, unlike operators in other programming languages, **do not return true or false. They always return one of the operands.**  
  
	- **OR ( | | ) operator** - If the first value is truthy, then the first value is returned. Otherwise, always the second value gets returned.  
  
	- **AND ( && ) operator** - If both the values are truthy, always the second value is returned. If the first value is falsy then the first value is returned or if the second value is falsy then the second value is returned.
	- **The ‘ == ‘ operator compares values and not types.**
	  ``````js
	var a = 12;
	var b = "12";
	a == b // Returns true because both 'a' and 'b' are converted to the same type and then compared. Hence the operands are equal.
	  ``````
# DOM 
- Document Object Model
- When the browser render a HTML document, it creates an object based on the HTML document called DOM
- Using DOM we can manipulate or change various elements inside the HTML document.
# Immediately Invoked Function
- An Immediately Invoked Function is a function that runs as soon as it is defined.
`````js  
(function(){ 
  // Do something;
})();
`````
# Scope
- Global scope
- Function scope
- Block scope
- Scope chain: If a small scope is access variable outside its scope, it will looking in the bigger scope.
  ``````js
	var y = 24;
	
	function favFunction(){
		var x = 667;
		var anotherFavFunction = function(){
			console.log(x); 
			// Does not find x inside anotherFavFunction, so looks for variable inside favFunction, outputs 667
		}
	
		var yetAnotherFavFunction = function(){
		console.log(y); 
		// Does not find y inside yetAnotherFavFunction, so looks for variable inside favFunction and does not find it, so looks for variable in global scope, finds it and outputs 24
		}
	
		anotherFavFunction();
		yetAnotherFavFunction();
	}
	favFunction();
  ``````
# Prototype
- Is a mechanism which make object in js inheritance a function from other object
- Every object in js have internal property called Prototype
- **A prototype is a blueprint of an object.** Prototype allows us to use properties and methods on an object even if the properties and methods do not exist on the current object.
# Callback 
- A callback is a function that will be executed after another function gets executed.
- When you pass functions as values, expect it to be **called back** later if necessary.
  ``````js
  function ask(question, yes, no) { 
	  if (confirm(question)) yes() 
	  else no(); 
  } 
  function showOk() { 
	  alert( "You agreed." ); 
  } 
  function showCancel() { 
	  alert( "You canceled the execution." ); 
  } // usage: functions showOk, showCancel are passed as arguments to ask 
  ask("Do you agree?", showOk, showCancel);
  `````` 
  
>**A function is a value representing an "action"**
>
>Regular values like strings or numbers represent the *data*
>A function can be perceived as an action
>We can pass it between variables and run when we want
- **A Function Expression is created when the execution reaches it and is usable only from that moment**
- **A Function Declaration can be called earlier than it is defined dual to hoisting**
- **In strict mode, when a Function Declaration is within a code block, it's visible everywhere inside that block. But not outside of it**

>**When to choose Function Declaration verus Function Expression?**
>
>As a rule of thumb, when we need to declare a function, the first to consider is Function Declaration syntax. It gives more freedom in how to organize our code, because we can call such functions before they are declared.
>
That’s also better for readability, as it’s easier to look up `function f(…) {…}` in the code than `let f = function(…) {…};`. Function Declarations are more “eye-catching”.
>
…But if a Function Declaration does not suit us for some reason, or we need a conditional declaration (we’ve just seen an example), then Function Expression should be used.
# Arrow function
- By general definition, the **this** keyword always refers to the object that is calling the function.  
- As you can see in the code below, `obj1.valueOfThis()` returns `obj1`, since `this` keyword refers to the object calling the function.  
- In the arrow functions, there is no binding of the `this` keyword.  
- The `this` keyword inside an arrow function, does not refer to the object calling it. It rather inherits its value from the parent scope which is the window object in this case.
  ``````js
	var obj1 = {
		valueOfThis: function(){
			return this;
		}
	}
	var obj2 = {
		valueOfThis: ()=>{
			return this;
		}
	}
	
	obj1.valueOfThis(); // Will return the object obj1
	obj2.valueOfThis(); // Will return window/global object
  ``````

# Class 
-   Unlike functions, classes are not hoisted. A class cannot be used before it is declared.
-   A class can inherit properties and methods from other classes by using the extend keyword.
-   All the syntaxes inside the class must follow the strict mode(‘use strict’) of javascript. Error will be thrown if the strict mode rules are not followed.
# Interaction
- alert
- prompt: shows a modal window with a text message, an input field for the visitor, and the buttons OK/Cancel. `[default]` is the default value when user doesn't input.
	``````js
	result = prompt(title, [default]);
	```````
- confirm: shows a modal window with a `question` and two buttons: OK and Cancel
  ``````js
  result = confirm(question);
  ``````
# Type conventions
- String convention: `String(value)`
- Numeric convention
	- Implicit
	  ``````js
	  alert( "6" / "2" ); // 3, strings are converted to numbers
	  ``````
	- Explicit
	  ``````js
	  let str = "123"; 
	  alert(typeof str); // string 
	  let num = Number(str); // becomes a number 123 
	  alert(typeof num); // number
	  `````` 
- Boolean convention
	- Values that are intuitively `empty` like `0`, `null`, `undefined` and `NaN` become `false`
	- Other become `true`
# Basic operators, math
- Exponentiation
	- `a ** b` is `a` power b, $a^b$
- Terms
	- unary: If it has a single operand. Like `x = -x`
	- binary: if it has 2 operands
	- operand: is what operators are applied to. For instance, in the multiplication of `5 * 2` there are two operands
		- The left operand is 5 and the right is 2. Sometimes, people call these "arguments" instead of "operands"
## Operator precedence
![[Pasted image 20220330133915.png]]
## Assignment
- An assignment `=` is also an operator.
- Example:
	-  `x = 2 * 2 + 1` the `2 * 2 + 1` is done first and then `=` is evaluated
## Chaining assignments
- Chained assignments evaluate from right to left. `a = b = c = 2 + 2` the rightmost expression `2 + 2` is evaluated and then assigned to the variables on the left `c` and `b` then `a`. At the end the variables share a single value.
- Split such code:
  ``````js
  c = 2 + 2;
  b = c;
  a = c;
  ``````
## Increment/decrement
- Postfix form: `a = counter++` return `counter` and then increase `counter` 
- Prefix form: `a = ++counter` increase `counter` and then return.
## Comma
- The comma operator allows us to evaluate several expressions, dividing them with a comma `,` each of them is evaluated, but only the result of the last one is returned.
  ``````js
  _let a = (1 + 2, 3 + 4);
  alert( a ); // 7 (the result of 3 + 4)
  ``````
  - Here, the first expression `1 + 2` is evaluated, and its result is thrown away. Then `3 + 4` is evaluated and returned as the result
  > **WARNING:** Comma has very low precedence
  > 
  > Please note that the comma operator has very low precedence, lower than `=` so parentheses are important in the example above.
  > Without them: `a = 1 + 2, 3 + 4` evaluate `+` first, summing the numbers into `a = 3, 7`, then the assignment operator `=` operator assigns `a = 3`, and the rest is ignored. It's like `(a = 1 + 2), 3 + 4` 
  - Why do we need an operator that throws away everything except the last expression?
	  - Sometimes, people use it more complex constructs to put several actions in one line
	    ``````js
	    // three operations in one line 
	    for (_a = 1, b = 3, c = a * b_; a < 10; a++) { 
		    ... 
		}
	    ``````
# String comparison
- **Algorithm**
	1. Compare the first character of both string
	2. If the first character from the first string is greater (or less) then the other string's, then the first string is greater (or less) then the second. We are done.
	3. Otherwise, if both strings' both character are the same, compare the second character the same way.
	4. Repeat until the end of either string.
	5. If both strings end at the same length, then they are equal. Otherwise, the longer string is greater.
>**Not a real dictionary, but Unicode order**
>
>The comparison algorithm given above is roughly equivalent to the one used in dictionaries or phone books, but it's not exactly the same
>For instance, case matters. A capital letter `"A"` is not equal to the lowercase `"a"`. Which one is greater? The lowercase `"a"`. Why? Because the lowercase character has a greater index in the internal encoding table JavaScript uses (Unicode).

![[Pasted image 20220330143230.png]]
![[Pasted image 20220330144030.png]]
![[Pasted image 20220330144043.png]]
# Logical operators
## `||` (OR)
- Evaluate from left to right if there are more than 2 "or" condition.
- Javascript will find the first truthy value and then return operand
## `&&` (AND)
- Evaluate from right to left if there are more than 2 "and" condition
- Javascript will find the first falsely value and then return operand
## Precedence 
- The precedence of AND is **higher** than OR
# Function
- If a same-named variable is declared inside the function then it shadows the outer one. For instance, in the code below, the function uses the local `username`. The outer one is ignored.
- Variables declared outside any function, such as the outer `userName` in the code above, are called *global*. Global variables are visible from any function
  ``````js
  let userName = 'John'; 
  function showMessage() { 
	  _let userName = "Bob"; // declare a local variable_ 
	  let message = 'Hello, ' + userName; // _Bob_ 
	  alert(message); 
  } // the function will create and use its own userName 
  showMessage(); 
  alert( userName ); // _John_, unchanged, the function did not access the outer variable
  ``````
  - It's a good practice to minimize the use of global variables.
  ## Parameters
  - If parameter is value then it will be passed as value in function
  - If parameter is reference type it will be passed as reference in function if you change its internal property value. If you change parameter itself, that won't affect the parameter.
	 ``````js
	function changeStuff(a, b, c)
	{
	  a = a * 10;
	  b.item = "changed";
	  c = {item: "changed"};
	}
	
	var num = 10;
	var obj1 = {item: "unchanged"};
	var obj2 = {item: "unchanged"};
	
	changeStuff(num, obj1, obj2);
	
	console.log(num);
	console.log(obj1.item);
	console.log(obj2.item);
    ``````
## Default values
``````js
function showMessage(from, _text = "no text given"_) { 
	alert( from + ": " + text ); 
} 
showMessage("Ann"); // Ann: no text given
``````

``````js
function showMessage(from, text = anotherFunction()) { 
	// anotherFunction() only executed if no text given 
	// its result becomes the value of text 
}
``````

>**Evaluation of default parameters**
>
In JavaScript, a default parameter is evaluated every time the function is called without the respective parameter.
In the example above, `anotherFunction()` isn't called at all, if the `text` parameter is provided.
On the other hand, it's independently called every time when `text` is missing.
## Alternative default parameters
- Sometimes it makes sense to assign default values for parameters not in the function declaration, but a later stage.
  ``````js
  function showMessage(text){
	  if(text === undefined){ // if the paramter is missing
		  text = 'empty message';
	  }
  }
  ``````
- Modern JS engines support the [[Nullish coalescing operator]] `??`, it's better when most falsely values, such as `0`, should be considered *normal*:
  ``````js
  function showCount(count){
	  // if count is undefined or null, show "unknow"
	  alert(count ?? "unknow");
  }
  showCount(0); // 0
  showCount(null); // unknow
  showCount(); // unknow
  
  ``````
## Return a value
- If a function does not return a value, it is the same as if it returns `undefined`
  ``````js
  function doNothing(){return;}
  function doNothingMore(){}
  alert(doNothing() === undefined); // true
  alert(doNothingMore() === undefined); // true
  ``````
- Return expression to wrap across multiple lines, we should wrap it at the same line as `return`
  ``````js
  return ( 
	  some + long 
	  + expression 
	  + or + whatever * f(a) + f(b) 
	  );
  ``````
## Function expressions
- In JS, a function is not a "magical language structure", but a special kind of value. 
- Function expression:
  ``````js
  let sayHi = function(){
	  alert("Hello");
  };
  ``````
- Function expression have a semicolon `;` at the end, but function declaration does not.
# Arrow functions
``````js
let func = (arg1, arg2, ..., argN) => expression;
``````
- Arrow function creates a function `func` that accepts arguments `arg1, arg2,.., argN` then evaluates the expression on the right side with their use and returns its result.
``````js
let double = n => n * 2; // roughly the same as: let double = function(n) { return n * 2 }
alert( double(3) ); // 6
``````  
### Multiline arrow functions
- Sometimes we need a more complex function, with multiple expressions and statements. In that case, but we can enclose them in curly braces, The major difference is that curly braces require a `return` within them to return a value.
  ``````js
  let sum = (a, b) => { // the curly brace opens a multiline function 
	  let result = a + b; 
	  return result; // if we use curly braces, then we need an explicit "return"
  }; 
  alert( sum(1, 2) ); // 3
  ``````

# Error objects:
- `message` the hunman-readable error message
- `name` error name
- `stack` the stack at the moment of  error creation