# Arrays
### Syntaxes for creating an empty array
``````js
let arr = new Array();
let arr = [];
``````
### Stack method 
- `push` appends an element to the end
- `pop` takes an element from the end
### Queue method
- `shift` get an element from the beginning, advancing the queue, so that the 2nd element becomes the 1st
- `unshift` add the element to be beginning of the array
## Internals
- Array is an object, so it behaves like an object
- Array store its elements in the contiguous memory area, one after another. So if you make array behaves like an object it will losse its optimizations
## Loop in array
- Using for loop and iterate by variable that smaller than array.length
  ``````js
  for(let i = 0; i < arr.length; i++){
	  //
  }
  ``````
  - Using `for...of`, i will be the value of the current array$[index]$
    ``````js
    for(let i of array){
	    alert(i) // array[index]
    }
    ``````
  - Using `for...in`, i will be the index of the current array
  ``````js
  for(let i in array){
	  alert(i) // index 0, 1, 2, 3,...
  }
  ``````
## Don't compare arrays with `==`
# Functions
## `splice`
- Insert, remove and replace elements
  ``````js
  arr.splice(start, deleteCount, elment1, ..., elementN)
  ``````
- It modifies  `arr` staring from the index `start` remove `deleteCount` elements and then inserts `element1, ..., elementN` at their place. Return the array of removed elements.
  ``````js
	let arr = ["I", "study", "JavaScript", "right", "now"];
	// remove 3 first elements and replace them with another
	arr.splice(0, 3, "Let's", "dance");
	alert( arr ) // now ["Let's", "dance", "right", "now"]
  ``````
- The `splice` method is also able to insert the elements without any removals. So that `deleteCount` will be 0.
- Negative indexes allowed. They specify the position from the end of the array.
  ``````js
	 let arr = [1, 2, 5];
	// from index -1 (one step from the end)
	// delete 0 elements,
	// then insert 3 and 4
	arr.splice(-1, 0, 3, 4);
	alert( arr ); // 1,2,3,4,5
  ``````
## `slice`
- `slice` is simpler than `splice`
``````js
arr.slice([start], [end]);
``````
- It returns a new array copying to it all item from index `start` to `end` (not include `end`). Both `start` and `end` can be negative, in that case position from array end is assumed.
``````js
let arr = ["t", "e", "s", "t"];
alert( arr.slice(1, 3) ); // e,s (copy from 1 to 3)
alert( arr.slice(-2) ); // s,t (copy from -2 till the end)
``````
# `concat`
- Create new array that includes values from other arrays and additional items.
  ``````js
  arr.concat(arg1, arg2, ...);
  ``````
- Object has a special `Symbol.isConcatSpreadable` property, then it's treated as an array `concat`
  ``````js
let arr = [1, 2];
let arrayLike = {
	 0: "something",
	 1: "else",
	 [Symbol.isConcatSpreadable]: true,
	 length: 2
};
alert( arr.concat(arrayLike) ); // 1,2,something,else
  ``````
## `forEach`
- Allows to run a function for every element of the array
  ``````js
 arr.forEach(function(item, index, array) {
   // ... do something with item 
 });
  ``````
## Searching in array
### `indexOf(item, from)`
- Looks for `item` starting from index `from` and return the index where it was found, otherwise `-1`
### `lastIndexOf(item, from)`
- Same as `indexOf` but looks for from right to left
### `includes(item, from)`
- Looks for `item` starting from `index` return `true` if found.
### `find(item)`
- Return `true` if item is found in the array, else return `undefined`
``````js
let users = [
	{id: 1, name: "John"},
	{id: 2, name: "Pete"},
	{id: 3, name: "Mary"}
];

let user = users.find(item => item.id == 1);
alert(user.name); // John
``````
### `findIndex(item)`
- Same as `find(item)` but return index and `-1` when nothing is found.
### `filter` 
- Retrun array of matching filter.
  ``````js
let users = [
{id: 1, name: "John"},
{id: 2, name: "Pete"},
{id: 3, name: "Mary"}
];

let someUsers = users.filter(item => item.id < 3);
alert(someUsers.length); // John
  ``````
## Transform an array
### `map`
- It calls the function for each element of the array and return the array of results
``````js
let lengths = ["Bilbo", "Gandalf", "Nazgul"].map(item => item.length);

alert(lengths); // 5,7,6
``````
### `sort(fn)`
- Returns the sorted array, but the returned value is usually ignored, as array itself is modified.
  ``````js
let arr = [ 1, 2, 15 ];
// the method reorders the content of arr
arr.sort();
alert( arr ); // 1, 15, 2
  ``````
- ***The items are shorted as strings by default***
- The `sort(fn)` method implements a generic sorting algorithm. We don't need to care how it works. It will walk the array, compare its elements using the provied function and reorder them, all we need is to provide the `fn` which does the comparison.
  ``````js
  arr.sort((a,b) => a - b);
  ``````
  - Use `localeCompare` for strings. For many alphabets, it's better to use `string.localeCompare` method to correctly sort letters, such as `Ö`
    ``````js
let countries = ['Österreich', 'Andorra', 'Vietnam'];
alert( countries.sort( (a, b) => a > b ? 1 : -1) ); // Andorra, Vietnam, Österreich (wrong)
alert( countries.sort( (a, b) => a.localeCompare(b) ) ); // Andorra,Österreich,Vietnam (correct!)
    ``````
### `reverse()`
- Returns the array after reversal
### `split`
- Return array from strings have been split from string by given delimiter `delim`
  ``````js
let arr = 'Bilbo, Gandalf, Nazgul, Saruman'.split(', ', 2); 
alert(arr); // Bilbo, Gandalf
  ``````
- `split` method has an optional second numberic argument - a limit on the array length.
### `join`
- Reverse to `split`. It creates a string from `arr`, glue between them.
  ``````js
  let arr = ['Bilbo', 'Gandalf', 'Nazgul']; 
  let str = arr.join(';'); // glue the array into a string using ; 
  alert( str ); // Bilbo;Gandalf;Nazgul
  `````` 
### `reduce`
``````js
let value = arr.reduce(function(accumlator, item, index, array){
	//
}, [initial]);
``````
- The function is applied to all array elements one after another and "carries on" its result to the next call.
	- `accumulator` the result of the previous function call, equals `initial` the first time (if `initial` is provided)
	- `item` the current array item
	- `index` its position
	- `array` 
``````js
let arr = [1, 2, 3, 4, 5]; // removed initial value from reduce (no 0) 
let result = arr.reduce((sum, current) => sum + current, 0); 
alert( result ); // 15
``````
### `Array.isArray`
- Array are based on objects
- If we use `typeof` the result will be `object`. So if you want to check the variable is array you need to use `Array.isArray()`
``````js
alert(Array.isArray({})); // false 
alert(Array.isArray([])); // true
``````
### Most methods support `thisArg`
- The value of `thisArg` parameter becomes `this` for `func`
``````js
arr.find(func, thisArg); 
arr.filter(func, thisArg); 
arr.map(func, thisArg);
``````

``````js
let army = {
 minAge: 18,
 maxAge: 27,
 canJoin(user) {
 return user.age >= this.minAge && user.age < this.maxAge;
 }
};

let users = [
 {age: 16},
 {age: 20},
 {age: 23},
 {age: 30}
];
// find users, for who army.canJoin returns true
let soldiers = users.filter(army.canJoin, army);

alert(soldiers.length); // 2
alert(soldiers[0].age); // 20
alert(soldiers[1].age); // 23
``````
![[Pasted image 20220401100717.png]]