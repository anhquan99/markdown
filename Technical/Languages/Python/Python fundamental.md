# Overview of Python
- Interpreted, high-level, general-purpose programming language
- Dynamically type and garbage-collected
- Support multiple programming paradigms
	- Procedure
	- OOP
	- Functional
- Default implementation: `CPython`
	- `Jython-Java`
	- `IronPython-.NET`
# Virtual Environment
- Virtual environment is an isolated environment
- We can config different environment with diffrents version to run python
- `virtualenv` is a built-in module to create virtual environment
# Data types
- Primitive
	- Integer
	- Float
	- String (Immutable)
	- Boolean
- Non-Primitive
	- List `[]`
	- Tuple (Immutable) `()`
	- Dictionary
	- Set `{}`
	- Frozen Set (Immutable)
# Tuple vs list
- List have some reference to object but tuple does not so the size of tuple will smaller than list and the iteration time will be smaller
# Scope
- Like scope in [[Javascript fundamental map| JS]]
- `global` keyword will use global variable
- `inner function` available in the function scope, we can use `nonlocal` keyword to access the outer function variable.
- We should not name the same name variable in the same file, it's hard to read.
# Lambda function
- It's an anonymous function 
# Packing and unpacking argurments
- Pass `*` as tuple or list to pack arguments into tuple
- Pass `**` dictionary o unpack arguemtn into dictionary
- Pass `\` as the positional argument
#  OOP
- `__` keyword is an internal property
- Use ABC (`abstract_method`) package for abstract class
# Closure
- Everything in Python are objects
- Nested function
- References a value in main scope
- Main function returns nested one
- Avoid use of global value
- Provide a form of data hiding
- Good to use when there few methods/attributes to define a class
- Inner scope variable can read variable pass from outside
# Generator
- It's a type of `Iterator`
- Save the state of the `Iterator`
- Use `yield` keyword to get data from the current state
- We don't have to implement `Iterator` class, `Generator` help us focus on internal, state and scope of variables 
- With generator, `yield` will stop after yield and continue with next variable
# Iterator
- Object generate state when iterate to next object then iterator will generate next state, that makes object light and fast.
- Lazy load next value
# Modules and Import
- `__init__.py` is the top-level package
# Plugins for python
- `pylint`
- `pycodestyle`