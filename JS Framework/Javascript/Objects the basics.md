# Objects
- Objects are used to store keyed collections of various data and more complex entities.
- An object can be created with figure brackets `{...}` with an optional list of properties. A property is a "key: value" pair, where `key` is a string (also called a "property name"), and `value` can be anything.
  ``````js
  var sample = {
	key1 : "value1",
	key2 : 2,
	key3 : 0.3,
	...
  }
  ``````
- An empty object ("empty cabinet") can be created using one of 2 syntaxes
  ``````js
  let user = new Object(); // "object contructor syntax"
  let user = {}; // "object litteral syntax"
  ``````
- To remove a property, we can use `delete` operator
  ``````js
  delete object.property;
  ``````
# Multiword
- We can use multiword property names, but then they must be quoted
  ``````js
  let user = {
	name: 'John',
	age : 30,
	"like birds": true  
  };
  ``````
- When we want to access multiword property we use square brackets
  ``````js
  let user = {};
  // set
  user["like birds"] = true;
  // get
  alert(user["like birds"]);
  // delete
  delete user["likes birds"];
  ``````

- Square brackets also provide a way to obtain the property name as the result of any expression - as opposed to literal string - like from a variable as follows
  ``````js
  let key = "likes birds";
  
  // same as user["likes birds"] = true;
  user[key] = true;
  ``````
- The variable `key` mat ve calculated at run-time or depend on the user input. And then we use it to access the property. That gives us a great deal of flexibility
  ``````js
  let use = {
	  name: "Quan",
	  age: 23
  };
  let key = prompt("What do you want to know about the user?", "name");
  
  // access by variable
  alert(user[key]);
  ``````
- The dot notation can not be used in a similar way
  ``````js
  let key = "name";
  alert(user.key); // undefined
  ``````
## Computed properties
- We can use square brackets in an object literal, when creating an object. That's called computed properties.
  ``````js
  let fruit = prompt("Which fruit to buy?", "apple"); 
  let bag = { 
	  [fruit]: 5, // the name of the property is taken from the variable fruit
   }; 
   alert( bag.apple ); // 5 if fruit="apple"
  ``````
  - Square brackets are much more powerful than the dot notation. They allow any property names and variables. But they are more cumbersome to write.
  - So most of the time, when property named are known and simple, the dot is used. And if we need something more complex, then we switch to square brackets.
## Property value shorthand
``````js
function makeUser(name, age) { 
	return { 
		name: name, 
		age: age, // ...other properties 
		}; 
	} 
let user = makeUser("John", 30); 
alert(user.name); // John
``````
## Property existence test, "in" operator
- Check if property is in the object
  ``````js
  let user = {name : "Quan", age : 30};
  
  alert( "age" in user); // true
  alert( "something" in user); // false
  alert(user.something) // undefind
  ``````
- Why use `in`. Because in some case we can assign `undefind` to property and when we access it returns `undefined` just like the way we access undefined property.
  ``````js
  let obj = { test: undefined}
  alert(obj.test); // undefined
  alert("test" in obj) // undefined
  ``````
## The "for ... in" loop
- Walk over all keys of an object
  ``````js
  for (key in object) { 
  // executes the body for each key among object properties 
  }
  ``````

``````js
let user = {
	name: "John", 
	age: 30, 
	isAdmin: true 
}; 
for (let key in user) { 
	// keys 
	alert( key ); // name, age, isAdmin 
	// values for the keys 
	alert( user[key] ); // John, 30, true 
}
``````
# Object references and copying
- A variable assigned to an object stores not the object itself, but its "address in memory" - in other words, "a reference" to it.
  ``````js
  let user = {
	  name: "John"
  };
  ``````
![[Pasted image 20220331105002.png]]
- When an object variable is copied, the reference is copied, but the object itself is not duplicated.
  ``````js
  let user = {name: "John"};
  let admin = user; // copy reference
  ``````
  ![[Pasted image 20220331105134.png]]
  ``````js
  let user = { name: 'John' };
  let admin = user;
  admin.name = 'Pete'; // changed by the "admin" reference
  alert(user.name); // 'Pete', changes are seen from the "user" reference
  ``````
## Cloning and merging, Object.assign
- If object contains only ***value type data***, when you clone that object using `for(key in object)` is equivalent to `Object.Assign`
  ``````js
let user = {
 name: "John",
 age: 30
};

let clone = {}; // the new empty object
// let's copy all user properties into it

for (let key in user) {
 clone[key] = user[key];
}
// now clone is a fully independent object with the same content
clone.name = "Pete"; // changed the data in it
alert( user.name ); // still John in the original object

let cloneUsingAssign = new Object();
cloneUsingAssign = Object.assign(cloneUsingAssign, user);
cloneUsingAssign = Object.assign({}, user); // equivalent to for loop
  ``````
- Syntax for `Object.assign`
  ``````js
  Object.assign(dest, [src1, src2, src3...])
  ``````
### Nested cloning
- We use the `for loop` to copy nested object because when the object is nested, the `Object.assign` will be copy the reference nested object.
## Shallow copy
- Copy the reference of the original variable
## Deep copy
- A deep copy means that all the values of the new variable are copied and disconnected from the original variable
## Spread operator
- A shallow copy method
  ``````js
  var user = { name: "Quan", Job: { JobName: "Dev", Level: "Fresher" }};
  var clone = {...user}; // clone from user
  
  var vehicle = {Name: "AB", Type: "Motobike"};
  var mergeClone = {...user, ...vehicle}; // clone and merge from vehicle
  
  user.Job.Level= "Senior";
  alert(clone.Job.Level); // return Senior
  ``````
- Can use for array
  ``````js
  const a = [1,2,3];
  let b = [...a];
  ``````
## Object method
- Basic
``````js
let user = {
	 name: "Quan",
	 age: 30
};
use.sayHi = function(){
	alert("Hello");
};
user.sayHi();
```````
- Pre-declared function
``````js
let user = {};
function sayHi(){
	//
}
user.sayHi = sayHi;
user.sayHi();
``````
- Shorthand method
``````js
user = {
	sayHi(){
		alert("Hello");
	}
};
``````
## This in method
- To access the object, a method can use the `this` key word.
![[Pasted image 20220331135144.png]]
- `this` is not bound
  ``````js
  function sayHi(){
	  alert(this.name); //  "this" keyword is not defined
  }
  let user = { name: "user"};
  let admin = {name: "admin"};
  
  user.f = sayHi;
  admin.f = sayHi;
  // these calls have different this 
  // "this" inside the function is the object "before the dot"
  user.f(); // John (this == user) 
  admin.f(); // Admin (this == admin) 
  admin['f'](); // Admin (dot or square brackets access the method – doesn't matter)
  ``````
- Arrow functions have no `this`. Arrows functions are special, they don't have their "own" `this`. If we reference `this` from such a function, it's taken from the outer "normal" function.
  ``````js
  let user = {
	  firstName: "Quan",
	  sayHi(){
		  let arrow = () => alert(this.firstName);
		  arrow();
	  }
  }
  user.sayHi();
  ``````
## Constructor functions
- Technically, constructor functions are regular functions. 
- Convention for constructor functions:
	- Named with capital letter first
	- They should be executed only with `new` operator
- When a function executed with `new`, it does these following steps:
	1. Create a new empty object and assigned to `this`.
	2. The function body executes.
	3. The value of `this` is returned.
``````js
function User(name){
	// this = {}; (imlicitly)

	// add properties to this
	this.name = name;
	this.job = "Admin";

	// return this; (implicitly)
}
``````
- `new` function can be wrapped in other `new` function.
## `new.target`
- We can check whether it was called with `new` or without it, using a special `new.target` property
  ``````js
function User(name) {

if (!new.target) { // if you run me without new

	return new User(name); // ...I will add new for you
}
this.name = name;
}

let john = User("John"); // redirects call to new User

alert(john.name); // John
  ``````
## Omitting parentheses
- We can omit parentheses after `new`, if it has no argument
  ``````js
  let user = new User;
  // same as
  let user = new User();
  ``````
## Optional chaining `?.`
- The optional chaining `?.` is a safe way to access nested object properties, even if an intermediate property doesn't exist.
  ``````js
  let user = {};
  
  alert(user.address.street); // error!
  alert(user?.address?.street); // undefined
  ``````
- We should use `?.` only where it’s ok that something doesn’t exist. For example, if according to our code logic `user` object must exist, but `address` is optional, then we should write `user.address?.street`, but not `user?.address?.street`. Then, if `user` happens to be undefined, we’ll see a programming error about it and fix it. Otherwise, if we overuse `?.`, coding errors can be silenced where not appropriate, and become more difficult to debug.