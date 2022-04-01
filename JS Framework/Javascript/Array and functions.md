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
- `shift` get an element from the begining, advancing the queue, so that the 2nd element becomes the 1st
- `unshift` add the element to be begining of the array
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
- It modilies `arr` staring from the index `start` remove `deleteCount` elements and then inserts `element1, ..., elementN` at their place. Return the array of removed elements.
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
## Seaching in array
### `indexOf`, `lastIndexOf` and `includes`
- 