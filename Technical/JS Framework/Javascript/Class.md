# Private
- Private start with `#`. They are only accessible from inside the class.
- Private fields are not available as `this[name]`
  ``````js
  class User { 
	  ... sayHi() {
	   let fieldName = "name";
	   alert(`Hello, ${_this[fieldName}`); 
	   } 
   }
  ``````
# Protected
- Protected properties are usually prefixed with an underscore `_`
- This is not enforced on the language level.
  ``````js
 class CoffeeMachine {
	 _waterAmount = 0;
	 set waterAmount(value) {
		 if (value < 0) {
		 value = 0;
		 }
		 this._waterAmount = value;
	 }
	 get waterAmount() {
		 return this._waterAmount;
	 }
	 constructor(power) {
	 this._power = power;
	 }
}
// create the coffee machine
let coffeeMachine = new CoffeeMachine(100);
// add water
coffeeMachine.waterAmount = -10; // _waterAmount will become 0, not -10
  ``````
# Mixins
- Mixin is a class containing methods that can be used by other classes without a need to inherit from it. Mixin provides methods that implement a certain behavior, but we do not use it alone, we use it to add the behavior to other classes.
 ``````js
 // mixin
let sayHiMixin = {
	sayHi() {
		alert(`Hello ${this.name}`);
	},
	sayBye() {
		alert(`Bye ${this.name}`);
	}
};
// usage:
class User {
	constructor(name) {
		this.name = name;
	}
}
// copy the methods
Object.assign(User.prototype, sayHiMixin);
// now User can say hi
new User("Dude").sayHi(); // Hello Dude!
 ``````
 