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
	- 0, 0n, -0, “”, null, undefined and NaN
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
	console.log(x); // Does not find x inside anotherFavFunction, so looks for variable inside favFunction, outputs 667
	}
	
	var yetAnotherFavFunction = function(){
	console.log(y); // Does not find y inside yetAnotherFavFunction, so looks for variable inside favFunction and does not find it, so looks for variable in global scope, finds it and outputs 24
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
# Arrow function
- By general definition, the **this** keyword always refers to the object that is calling the function.  
- As you can see in the code above, **obj1.valueOfThis()** returns obj1, since **this** keyword refers to the object calling the function.  
- In the arrow functions, there is no binding of the **this** keyword.  
- The **this** keyword inside an arrow function, does not refer to the object calling it. It rather inherits its value from the parent scope which is the window object in this case.
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