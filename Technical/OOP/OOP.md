# <mark style="background: #ADCCFFA6;">Define Object-Oriented Programming (OOP)</mark>
- It is the programming paradigm that is defined using objects. Objects can be considered as real world instances of entities like class, that have some characteristics and behaviors.
# <mark style="background: #ADCCFFA6;">Why OOP</mark>
- Allows clarity in programming, thereby allowing simplicity in solving complex problems
- Reuse code through inheritance.
- Data and code are bound together by encapsulation
- Allows data hiding
- Problem can be divided into different part making it simple to solve
- Entities can have multiple forms through polymorphism
# <mark style="background: #ADCCFFA6;">Limitation of OOP</mark>
- Not suitable for small problem
- Intensive testing
- More time to solve the problem
- Proper planning
- Think of solving a problem in terms of objects
# <mark style="background: #ADCCFFA6;">Principles</mark>
- <mark style="background: #D2B3FFA6;">Encapsulation:</mark> all important information is contained inside an object and only select information is exposed. Encapsulation is achieved when each object keeps its state **private**, inside a class. Other objects don't have direct access to this state. Instead, they can only call a list of public methods.
	- <mark style="background: #FF5582A6;">Example:</mark> A cat can have meow() method private because no object can tell a cat to meow()
- <mark style="background: #D2B3FFA6;">Abstraction:</mark> Applying abstract means that each object should *only* expose a high-level mechanism for using it. This mechanism should hide internal implementation details, reveal it relevant operations for other objects.
	- <mark style="background: #FF5582A6;">Example:</mark> Functions in a phone are complex. Users don't need to know how phone work under the hood. When the implementation changes, it rarely affects the abstraction. 
- <mark style="background: #D2B3FFA6;">Inheritance:</mark> Objects are often very similar. They share common logic. But they are not entirely the same. So to reuse the common logic and extract the unique logic into a separate class, this can be achieved by inheritance. So the child class will inherit from another parent class. This way, we form a hierarchy. The child class reuses all fields and methods of the parent class and can implement its own (unique part)
	- <mark style="background: #FF5582A6;">Example:</mark> Student and Person have the same common as they are a person have the same name field, id,... . But each of them have their own unique properties, Student have school id and grades but Person don't have. 
- <mark style="background: #D2B3FFA6;">Polymorphism:</mark> gives a way to use a class exactly like its parent ,so there's no confusion with mixing types. But each child class keeps its own methods as they are.
	- <mark style="background: #FF5582A6;">Example:</mark> Figure class have CalculateSurface() and Calculate Perimete(). Triangle, Circle and Rectangle have the same methods as Figure but each of them have their own implement.
- <mark style="background: #D2B3FFA6;">Inheritance vs Polymorphism: </mark>
	- Inheritance: it's about using structure and behavior of the parent class
	- Polymorphism: it's about changing the behavior of a parent class in the child class.
# <mark style="background: #ADCCFFA6;">Other alternative methods:</mark>
- Functional programming: only have functions.
- Procedure programming: contain data and procedure.
- Structure programming: have only 1 structure.
# <mark style="background: #ADCCFFA6;">Why is OOP so popular?</mark>
- Help users to maintain code easily. 4 principles of the OOP make it easy to solve complex scenarios. 
# <mark style="background: #ADCCFFA6;">Object</mark>
- Is a real world entity which is the basic unit of OOP.
# <mark style="background: #ADCCFFA6;">Class</mark>
- Is the blueprint of a class which we use to create objects.
# <mark style="background: #ADCCFFA6;">Inheritance</mark>
-  <mark style="background: #D2B3FFA6;">Types:</mark>
	- Single
	- Multiple: 
	- Multilevel: inherit from class that has inherited from other class
	- Hierarchical: where parent class has more than 1 child class
	- Hybrid: Multilevel combine with Multiple
- <mark style="background: #D2B3FFA6;">Limitation:</mark>
	- Tightly coupled
	- Modifications in parent class will make changes in child class
# <mark style="background: #ADCCFFA6;">Polymorphism</mark>
- <mark style="background: #D2B3FFA6;">Types:</mark>
	- Dynamic: occurs at runtime, overriding - a child class have same methods but has its own implementation. 
	- Static: occurs at compile time, overloading - functions with same name but have different parameters.
# Encapsulation
- <mark style="background: #D2B3FFA6;">Access specifiers</mark>
	- public
	- private
	- protected
# <mark style="background: #ADCCFFA6;">Abstraction</mark>
- <mark style="background: #D2B3FFA6;">Achieve through:</mark>
	- Abstract class: consist of abstract methods. You can define methods in abstract class.
	- Abstract method: methods are declared but not defined, child class has to implement abstract methods.
- <mark style="background: #D2B3FFA6;">Note:</mark> abstract class can't have instance
- <mark style="background: #D2B3FFA6;">Interface:</mark> Allow you to declare methods without define. Interface is not like class, only contain declare methods. A class implements an interface has to define methods of the interface. 
# <mark style="background: #ADCCFFA6;">Constructor</mark>
- Is a function have the same name with the class, used to initialize objects of that class.
- <mark style="background: #D2B3FFA6;">Types:</mark>
	- Default
	- Parameterized
	- Copy: create a new object from an existing one.
	- Static: Initializing static properties.
	- Private
# <mark style="background: #ADCCFFA6;">Garbage Collection (GC)</mark>
- An implementation of automatic memory management, frees up space occupied by objects that are no longer in existence.
# <mark style="background: #ADCCFFA6;">Stack and heap</mark>
- <mark style="background: #D2B3FFA6;">Stack:</mark> every function when called will be given a memory space in stack to save local variable, when the function is finished that memory space is free.
- <mark style="background: #D2B3FFA6;">Heap:</mark> use to dynamic allocation of memory space when an object is created with key word `new`. The allocation of heap is handled separately
# <mark style="background: #ADCCFFA6;">Error vs Exception</mark>
- Error: problems should not be encountered by applications
- Exception: Conditions that an application might try to catch.
# [[Object Oriented Analysis and Design]]