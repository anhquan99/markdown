# KISS (Keep it simple, stupid)
- Write code simple as possible
# DRY (Don't repeat your self)
# YAGNI (You Aren't Gonna Need it)
- Until need to use that function you don't need to write that function
# SOLID
- Single responsibility
	- A class should have 1 responsibility.
- Open and close
	- Open for extension but close for modification
- Liskov substitution
	- An object of child class can replace parent class but not changes the rightness of the program.
	- ![[Pasted image 20220312203521.png]]
- Interface Segregation
	- Divide big interface into smaller interfaces to have specific purpose of that interface
- Dependency inversion
	- Higher module should not depend on lower module
	- Interface (abstraction) should not be specific.
	- ![[Pasted image 20220312204047.png]]
# SoC (Separation of Concerns)
- Divide application into small parts, each part handle 1 specific problem
- Help:
	- Maintain code easier
	- Easier to scale and make changes to the application
	- **Application:**
		- MVC
		- HTML, CSS, JS
		- Microservice architecture

# Avoid Premature Optimization
# Law of Demeter
- Talk only to your close friends. Don't talk to stranger
- Example: take O->P->Q
	- O don't know anything about Q, so this case is violated Law of Demeter
	- ![[Pasted image 20220312205522.png]]
	- ![[Pasted image 20220312205727.png]]