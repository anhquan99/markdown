# Map
- Map is a collection of keyed data item, just like an Object. But the main difference is that `Map` allows keys of any type
## Function of map
- `new Map()` creates new map
- `map.set(key, value)` stores value by the key
- `map.get(key)` returns value by the key
- `map.has(key)` returns `true` if `key` exists
- `map.delete(key)` removes the value by key
- `map.clear()` clear map
- `map.size` returns the size of map
### `map[key]` isn't the right way to use map
- Although `map[key]` also works, this treating `map` as a plain JS object. So we should use `map` method: `set`, `get`, ...
### Map can also use objects as keys
![[Pasted image 20220401105849.png]]
### Iteration over Map
- `map.keys()` returns an iterable for keys
- `map.values()` return an iterable for values
- `map.entries()` returns an iterable for entries `[key, value]`, it's used by default in `for..of`
### `Object.fromEntrie` object from map
``````js
let prices = Object.fromEntries([ 
	['banana', 1], 
	['orange', 2], 
	['meat', 4] ]); // now prices = { banana: 1, orange: 2, meat: 4 } 
alert(prices.orange.entries()); // 2
``````
# Set
- Special collection - "set of values" (withou keys), where each value may occur only once..
## Set functions
- `new Set(iterable)`
- `set.add(value)`
- `set.delete(value`
- `set.has(value)`
- `set.clear()` clear everything from set
- `set.size` count elements from set
# The itertation like Map
# WeakMap
- Key must be objects, not primitive values
- `WeakMap` does not support iteration and methods `keys()` , `values()`, `entries()`
- The main area of application for `WeakMap` is an additional *data storage*
- We put data to a `WeakMap`, using the object as the key when the object is garbage collected, that data will automatically disappear as well.
``````js
let cache = new Map();
  

// calculate and remember the result

function process(obj) {

 if (!cache.has(obj)) {

 let result = /* calculations of the result for */ obj;
	 cache.set(obj, result);
 }

 return cache.get(obj);

}
  

// Now we use process() in another file:
  

// 📁 main.js

let obj = {/* let's say we have an object */};

let result1 = process(obj); // calculated
  

// ...later, from another place of the code...

let result2 = process(obj); // remembered result taken from cache
  

// ...later, when the object is not needed any more:

obj = null;
  

alert(cache.size); // 1 (Ouch! The object is still in cache, taking memory!)

``````

``````js
// 📁 cache.js

let cache = new WeakMap();

// calculate and remember the result

function process(obj) {

 if (!cache.has(obj)) {

 let result = /* calculate the result for */ obj;

		cache.set(obj, result);
	 }
	
	 return cache.get(obj);
}

// 📁 main.js

let obj = {/* some object */};

let result1 = process(obj);

let result2 = process(obj);
  

// ...later, when the object is not needed any more:

obj = null;

// Can't get cache.size, as it's a WeakMap,

// but it's 0 or soon be 0

// When obj gets garbage collected, cached data will be removed as well
``````
# `WeakSet` behaves similarly to `WeakMap`
