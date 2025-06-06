# Prototype
- Like inheritance, we take something and extend it.
- In JS, objects have a special property `[[Prototype]]`, that is either `null` or references another object. That object is called "a prototype"
  ![[Pasted image 20220401162305.png]]
- When we read a property from `object`, and it's missing, JS automatically takes it from the prototype. In programming, this is called "prototype inheritance".
- The property `[[Prototype]]` is internal and hidden, but there are many ways to set it.
 ``````js
let animal = {
	eats: true
};

let rabbit = {
	jumps: true
};

rabbit.__proto__ = animal; // sets rabbit.[[Prototype]] = animal

alert(rabbit.eats); // true
alert(rabbit.jumps); // true
  ``````

![[Pasted image 20220401162735.png]]
- Here we can say that `animal` is the prototype of `rabbit` prototypically ingerits from `animal`
- The prototype can be chaining. So when access an property is not in the object, JS will find that property in the inner `[[Prototype]]`
  ![[Pasted image 20220401163536.png]]
## Limitation
- The references can't go in cricles
- The value of `__proto__` can be either an object or `null`. Other types are ignored.
## `__proto__` is a historical getter/setter for `[[Prototype]]` 
- The `__proto__` is not the same as the internal `[[Prototype]]` property.
- `__proto__` property is a bit outdated. It exists for historical reasons, modern JS suggests that we should use `Object.getPrototypeOf/Object.setPrototypeOf`
## The value of `this`
- `This` is not affected by prototypes at all.
- **No matter where the method is found: in an object or its prototype. In a method call `this` is always the object before the dot**
``````js
let animal = {
	walk() {
		if (!this.isSleeping) {
		alert(`I walk`);
		}
	},
	sleep() {
		this.isSleeping = true;
	}
};

let rabbit = {
	name: "White Rabbit",
	__proto__: animal
};
// modifies rabbit.isSleeping

rabbit.sleep();

alert(rabbit.isSleeping); // true
alert(animal.isSleeping); // if animal call sleep it will not be undefined 
// undefined (no such property in the prototype
``````
![[Pasted image 20220401165957.png]]
## `For...in` loop
- Iterates over inherited properties.
## `obj.hasOwnProperty(key)`
- Return `true` if `obj` has its own property
 ``````js
let animal = {
	eats: true
};

let rabbit = {
	jumps: true,
	__proto__: animal
};

for(let prop in rabbit) {

	let isOwn = rabbit.hasOwnProperty(prop);
	if (isOwn) {
		alert(`Our: ${prop}`); // Our: jumps
	} else {
	alert(`Inherited: ${prop}`); // Inherited: eats

	}
}
  ``````
## F.prototype - function prototype
``````js
let animal = {

	eats: true

};

function Rabbit(name) {

	this.name = name;

}

Rabbit.prototype = animal;

let rabbit = new Rabbit("White Rabbit"); //  rabbit.__proto__ == animal

alert( rabbit.eats ); // true
``````

  ![[Pasted image 20220401172538.png]]
# Prototype là gì?
- Là cơ chế mà các object trong JS kế thừa các tính năng từ một object khác. Tất cả các object trong JS đều có một prototype, và các object này kế thừa các thuộc tính và phương thức từ prototype của mình
- Protype bản thân cũng là 1 object trong JS
- Function trong JS cũng được gọi là 1 object, và nó cũng có thuộc tính prototype, bản thân thuộc tính này mang giá trị là 1 object
- Object trong JS có prototype attribute trỏ tới prototype object mà nó kế thừa thuộc tính. Ta dùng `__proto__` để truy cập tới prototype object.
``````js
//Tạo ra 1 hàm khởi tạo cơ sở function 
Animal(_age){ this.age = _age; }
//Có thể thêm thuộc tính vào thuộc tính prototype của hàm khởi tạo 
Animal.prototype.showAge = function(){ 
	console.log( this.age ); 
}; 
//Tạo ra 1 hàm khởi tạo con (sẽ dùng để kế thừa hàm cơ sở) 
function Dog(_color){ 
	this.color = _color; 
} 
//Thực hiện kế thừa, gán hàm khởi tạo của Animal cho prototype của 
Dog Dog.prototype = new Animal(); 
Dog.prototype.showColor = function(){ 
	console.log( this.color ); 
}; 
//Kiểm tra sự kế thừa 
var chophuquoc = new Dog('yellow'); 
chophuquoc.age = 3; 
chophuquoc.showAge(); //3  
chophuquoc.showColor(); //yellow
``````
# Closures
- Closure là những function tham chiếu đến các biến tự do (free variable) tách biệt. Nói cách khác, function được định nghĩa trong closure sẽ ghi nhớ mội trường trong nó được tạo ra
``````js
function numberGenerator() {
  // Local free variable that ends up within the closure
  var num = 1;
  function checkNumber() { 
    console.log(num);
  }
  num++;
  return checkNumber;
}

var number = numberGenerator();
number(); // 2

``````
# Closures vs prototype
- Định nghĩa đối tượng closures nhanh hơn
- Khả năng tiết kiệm bộ nhớ và khởi tạo đối tượng mới prototype nhanh hơn
- Truy cập hàm (getter, setter) prototype nhanh hơn