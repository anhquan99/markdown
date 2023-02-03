# Define Object-Oriented Programming ([[OOP]])
- It is the programming paradigm that is defined using objects. Objects can be considered as real world instances of entities like class, that have some characteristics and behaviors.
# Why OOP
- Allows clarity in programming, thereby allowing simplicity in solving complex problems
- Reuse code through inheritance.
- Data and code are bound together by encapsulation
- Allows data hiding
- Problem can be divided into different part making it simple to solve
- Entities can have multiple forms through polymorphism
# Limitation of OOP
- Not suitable for small problem
- Intensive testing
- More time to solve the problem
- Proper planning
- Think of solving a problem in terms of objects
# Principles
- <span style="color:aqua; font-weight:bold">Encapsulation:</span> all important information is contained inside an object and only select information is exposed. Encapsulation is achieved when each object keeps its state **private**, inside a class. Other objects don't have direct access to this state. Instead, they can only call a list of public methods.
	- <span style="color:yellow; font-weight:bold">Example:</span> A cat can have meow() method private because no object can tell a cat to meow()
- <span style="color:aqua; font-weight:bold">Abstraction:</span> Applying abstract means that each object should *only* expose a high-level mechanism for using it. This mechanism should hide internal implementation details, reveal it relevant operations for other objects.
	- <span style="color:yellow; font-weight:bold">Example:</span> Functions in a phone are complex. Users don't need to know how phone work under the hood. When the implementation changes, it rarely affects the abstraction. 
- <span style="color:aqua; font-weight:bold">Inheritance:</span> Objects are often very similar. They share common logic. But they are not entirely the same. So to reuse the common logic and extract the unique logic into a separate class, this can be achieved by inheritance. So the child class will inherit from another parent class. This way, we form a hierarchy. The child class reuses all fields and methods of the parent class and can implement its own (unique part)
	- <span style="color:yellow; font-weight:bold">Example:</span> Student and Person have the same common as they are a person have the same name field, id,... . But each of them have their own unique properties, Student have school id and grades but Person don't have. 
- <span style="color:aqua; font-weight:bold">Polymorphism:</span> gives a way to use a class exactly like its parent ,so there's no confusion with mixing types. But each child class keeps its own methods as they are.
	- <span style="color:yellow; font-weight:bold">Example:</span> Figure class have CalculateSurface() and Calculate Perimete(). Triangle, Circle and Rectangle have the same methods as Figure but each of them have their own implement.
- <span style="color: orange; font-weight: bold">Inheritance vs Polymorphism:</span> 
	- <span style="color:aqua; font-weight:bold">Inheritance:</span> it's about using structure and behavior of the parent class
	- <span style="color:aqua; font-weight:bold">Polymorphism:</span> it's about changing the behavior of a parent class in the child class.
# Other alternative methods:
- Functional programming: only have functions.
- Procedure programming: contain data and procedure.
- Structure programming: have only 1 structure.
# Why is OOP so popular?
- Help users to maintain code easily. 4 principles of the OOP make it easy to solve complex scenarios. 
# Object
- Is a real world entity which is the basic unit of OOP.
# Class
- Is the blueprint of a class which we use to create objects.
# Inheritance
- ## Types:
	- Single
	- Multiple: 
	- Multilevel: inherit from class that has inherited from other class
	- Hierarchical: where parent class has more than 1 child class
	- Hybrid: Multilevel combine with Multiple
- ## Limitation:
	- Tightly coupled
	- Modifications in parent class will make changes in child class
# Polymorphism
- ## Types:
	- Dynamic: occurs at runtime, overriding - a child class have same methods but has its own implementation. 
	- Static: occurs at compile time, overloading - functions with same name but have different parameters.
# Encapsulation
- ## Access specifiers
	- public
	- private
	- protected
# Abstraction
- ## Achieve through:
	- Abstract class: consist of abstract methods. You can define methods in abstract class.
	- Abstract method: methods are declared but not defined, child class has to implement abstract methods.
- <span style="color:yellow; font-weight: bold">Note:</span> abstract class can't have instance
- **Interface:**  Allow you to declare methods without define. Interface is not like class, only contain declare methods. A class implements interface has to define methods of the interface. 
# Constructor
- Is a function have same name with the class, used to initialize initialize objects of that class.
- ## Types:
	- Default
	- Parameterized
	- Copy: create a new object from an existing one.
	- Static: Initializing static properties.
	- Private
# Garbage Collection (GC)
- An implementation of automatic memory management, frees up space occupied by objects that are no longer in existence.
# Stack and heap
- **Stack:** every function when called will be given a memory space in stack to save local variable, when the function is finished that memory space is free.
- **Heap:** use to dynamic allocation of memory space when an object is created with key word `new`. The allocation of heap is handled separately
# Error vs Exception
- **Error:** problems should not be encountered by applications
- **Exception:** Conditions that an application might try to catch.
