# Numbers
- ***Types:***
	- Regular numbers are stored in 64-bit format
	- Bigint numbers to represent integers of arbitrary length. Because a regular number can't safely exceed $2^53$ or be less than $-2^53$
``````js
let billion = 1000000000;
// same as
billion = 1_000_000_000; // syntactic sugar
// JS engine ignores _ between digits
``````
- JS can shorten a number by appending the letter `e`
``````js
let billion = 1e9; // 1 billion, literally 1 and 9 zeroes

let mcs = 0.000001;
// same as
mcs = 1e-6; // six zeroes to the left from 1
``````
## Hex
- Hex number are widely use. Represent `0x`
``````js
alert(0xff); // 255
``````
## Binary
- Represent `0b`
``````js
let a = 0b11111111; // 255
``````
## Octal
- Represent `0o`
``````js
let b = 0o377 // 255
``````
## toString(base)
- The method `num.toString(base)` returns a string representation of `num` in the numeral system with the given `base`
  ``````js
  let num = 255;
  alert(num.toString(16)); // ff
  alert(num.toString(2)); // 11111111
  
  // short version of toString(base)
  alert(12345..toString(36)); // 2n9c
  ``````
## `isFinite` and `isNaN`
- `Infinity` and `-Infinity` is a special numeric value that is greater (less) than anything
- `NaN` represents an error.
``````js
alert( isNaN(NaN) ); // true 
alert( isNaN("str") ); // true

alert( NaN === NaN ); // false

alert( isFinite("15") ); // true 
alert( isFinite("str") ); // false, because a special value: NaN 
alert( isFinite(Infinity) ); // false, because a special value: Infinity
``````
![[Pasted image 20220331165107.png]]
