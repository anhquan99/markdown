# Closure

## Definition
- A closure is a programming pattern where an inner function retains access to the variables of its outer (enclosing) scope, even after that outer function has finished execution.
- This occurs because the inner function "captures" or closes over its lexical environment, preserving references to those variables rather than copying their values at definition time.

## Core Mechanics
- **Lexical Scoping:** Variable visibility is determined by the static structure of the source code. An inner block or function can access variables declared in parent scopes.
- **First-Class Functions:** Languages supporting closures treat functions as first-class citizens, meaning they can be assigned to variables, passed as arguments, and returned from other functions.
- **Environment Retention:** Captured variables outlive the stack frame of the outer function. Runtimes manage this by moving variables from the stack to the heap when a closure outlives its creator.

## Primary Use Cases
- **Data Encapsulation:** Creating private state that cannot be accessed or modified directly from the global scope, effectively implementing information hiding without heavy class boilerplate.
- **Function Factories:** Generating specialized functions dynamically by passing configuration parameters into an outer creator function.
- **Asynchronous Callbacks:** Preserving context and local state across asynchronous execution boundaries, event listeners, or deferred timers.
