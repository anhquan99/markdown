# ES5
## `use strict`
- Helps you to write cleaner code, like preventing you from using undeclared variables.
## Property access on Strings
``````js
var str = "Hello world";
str[0]; // H
``````
## String over multiple lines
- Allows string literals over multiple lines if escaped with a backslash
``````js
"Hello \
Dolly!";
``````

>The \ method might not have universal support. Older browsers might treat the spaces around the backslash differently. Some older browsers do not allow spaces behind the \ character
- Safer way to break up a string is using `+` string addition.
## Reserved Words as Property names
``````js
var obj = {name: "john", new: "yes"}
``````
## String `trim()`
## [[Array and functions#Array isArray | Array.isArray]]
## [[Array and functions#forEach | Array forEach()]]
## [[Array and functions#map | Array map()]]
## [[Array and functions | Array filter()]]
## [[Array and functions#reduce | Array reduce()]]
## Array `every()`
- Return boolean value, check every element in array match the condition 
## Array `some()`
- Return `true` if some element in array match the condition, else `false`
## [[Array and functions#indexOf item from | Array indexOf()]]
## [[Array and functions#lastIndexOf item from | Array lastIndexOf()]]
## [[JSON#JSON stringify object | JSON.stringify()]]
## `JSON.parse`
- Used to convert the text into a JS object
  ``````js
  var obj = JSON.parse('{"name":"John", "age":30, "city":"New York"}');
  ``````
## `Date.now()`
- Returns the number of microseconds since zero date
## Date `toISOString()`
- `toISOString()` method converts a Date object to a string, using ISO standard format
  ``````js
  const d = new Date();  
  alert(d.toISOString()); // 2022-04-01T07:43:54.762Z
  ``````
## Date `toJSON()`
- `toJSON()` converts a Date object into a string, formatted as a JSON date
- JSON dates have the same format as the ISO-8601 standard `YYYY-MM-DDTHH:mm:ss.sssZ`
  ``````js
  d = new Date();
  console.log(d.toJSON());
  ``````
## Property Getters and Setters
- ES5 lets you define methods with a syntax that looks like getting or setting a property
  ``````js
var person = {  
	firstName: "John",  
	lastName : "Doe",  
	get fullName() {  
	return this.firstName + " " + this.lastName;  
	}  
};
  ``````
## `Object.defineProperty()`
- It lets you define an object property and/or change a property's value and/or metatdata
``````js
// Create an Object:  
var person = {  
 firstName: "John",  
 lastName : "Doe",  
 language : "NO",  
};  
  
// Change a Property:  
Object.defineProperty(person, "language", {  
 value: "EN",  
 writable : true,  
 enumerable : true,  
 configurable : true  
});  
  
// Enumerate Properties  
var txt = "";  
for (var x in person) {  
 txt += person[x] + "<br>"; // John, Doe, EN
}

Object.defineProperty(person, "language", {
  value: "EN",
  writable : true,
  enumerable : false,
  configurable : true
});

// Change a Property:
Object.defineProperty(person, "language", {
  value: "EN",
  writable : true,
  enumerable : false,
  configurable : true
});
// Enumerate Properties
txt = "";
for (var x in person) {
  txt += person[x] + "<br>";  // John, Doe
}  
``````
## ES5 Object Methods
``````js
// Create object with an existing object as prototype  
Object.create(parent, donor)  
  
// Adding or changing an object property  
Object.defineProperty(object, property, descriptor)  
  
// Adding or changing object properties  
Object.defineProperties(object, descriptors)  
  
// Accessing Properties  
Object.getOwnPropertyDescriptor(object, property)  
  
// Returns all properties as an array  
Object.getOwnPropertyNames(object)  
  
// Accessing the prototype  
Object.getPrototypeOf(object)  
  
// Returns enumerable properties as an array  
Object.keys(object)
``````
### Protecting objects
``````js
// Prevents adding properties to an object  
Object.preventExtensions(object)  
  
// Returns true if properties can be added to an object  
Object.isExtensible(object)  
  
// Prevents changes of object properties (not values)  
Object.seal(object)  
  
// Returns true if object is sealed  
Object.isSealed(object)  
  
// Prevents any changes to an object  
Object.freeze(object)  
  
// Returns true if object is frozen  
Object.isFrozen(object)
``````
## `Bind()`
- `Bind()` method, an object can borrow a method from another object

``````js
const person = {
  firstName:"John",
  lastName: "Doe",
  fullName: function() {
    return this.firstName + " " + this.lastName;
  }
}

const member = {
  firstName:"Hege",
  lastName: "Nilsen",
}

let fullName = person.fullName.bind(member);

console.log(fullName()); // Hege Nilsen
``````