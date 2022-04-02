# ES6
## [[Javascript Fundamentals#Var vs let vs const | let and const]]
## [[Javascript Fundamentals#Arrow functions]]
## `For...of` loop
- `For...of` lets you loop over data structures that are iterable such as Arrays, Strings, Maps, NodeLists, and more.
``````js
for(variable of iterable){
	//
}
``````
## [[Map and Set | Map and Set]]
## Classes
- JS classes are templates for JS object
``````js
class ClassName{
	constructor(){...}
}
``````
## Promises
## [[Symbol]]
## Default parameter values
``````js
function myFunction(x, y = 10) {  
  // y is 10 if not passed or undefined  
  return x + y;  
}  
myFunction(5); // will return 15
``````
## Function Rest Parameter
- Allows a function to treat an indefinite number of arguments as an array
  ``````js
function sum(...args) {  
	let sum = 0;  
	for (let arg of args) sum += arg;  
	return sum;  
}  

let x = sum(4, 9, 16, 25, 29, 100, 66, 77);
  ``````
# `String.includes()`
- Return `true` if a string contains a specified value, otherwise `false`
## `String.startWith()`
- `startWith()` method return `true` if a string begins with a specified value, otherwise `false`
## `String.endsWith()`
## `Array.from()`
- Returns an Array object from any object with a length property or any iterable object.
## [[Array and functions#Searching in array | Searching in array]]
## Math Methods
-   `Math.trunc(x)` returns the integer part of x
-   `Math.sign(x)` returns if x is negative, null or positive 
-   `Math.cbrt(x)` returns the cuve root of x, $x^3$
-   `Math.log2(x)` returns base 2 logarithms of x
-   `Math.log10(x)` return base 10 logarithms of x
## Number properties
-  `EPSILON` represents the difference between 1 and the smallest floating point number greater than 1.
-  `MIN_SAFE_INTEGER` 
-  `MAX_SAFE_INTEGER`
## Number methods
-  `Number.isInteger()`
-  `Number.isSafeInteger()` This is safe: 9007199254740991. This is not safe: 9007199254740992
## Global methods
- `isFinite()` check if it is `Infinity` or `NaN`
- `isNaN()` check if it is `NaN`
## [[Objects the basics#Object keys values entries | Object entries()]]
