# Symbol
- Is a primitive data.
- To create a Symbol, you can use `Symbol()` and it doesn't include `new` keyword
- Symbol is unique
- Symbol can not be converted to string
- Global Symbol used when you want to have the same description reference to only one Symbol you use `Symbol.for(key)` 
 ``````js
// Tạo một Symbol mới với description là "id"
let id1 = Symbol("id");

/*
 * Tìm trong Global một Symbol với key là "id".
 * Nếu Symbol chưa tồn tại thì tạo mới một Symbol
 */
let id2 = Symbol.for("id");

/*
 * Tiếp tục tìm trong Global một Symbol với key là "id".
 * Lần này thì Symbol đã tồn tại rồi, nên sẽ trả về Symbol trên.
 */
let id3 = Symbol.for("id");

// Kết quả
console.log(id1 === id2); // => false
console.log(id2 === id3); // => true

  ``````
- `Symbol.keyFor` return key value of the Symbol. However this method only apply for Symbol which created from `Symbol.for(key)`
## Usage
- Symbol can be used as key in property of object. However you can't use `for...in` to iterate the object. To iterate object have symbol you can use `Object.getOwnPropertySymbols()`
```js
const id = Symbol("id");
const obj = {
  [id]: "abc123",
};

console.log(obj);
// => {Symbol(id): "abc123"}
```

```js
const id = Symbol("id");
const name = Symbol("name");

const obj = {
  [id]: "abc123",
  [name]: "obj",
  y: 2,
};
```
- Symbol used to  avoid name conflicts
  ![[Pasted image 20220331153512.png]]
  ![[Pasted image 20220331153527.png]]
  