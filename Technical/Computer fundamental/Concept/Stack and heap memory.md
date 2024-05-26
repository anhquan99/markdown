- Ref: https://medium.com/@CodeWithHonor/c-stack-vs-heap-memory-f8a737af9919
- Both stored in RAM.
# Stack

- Behave like [[Stack]].
- Each time the function is called, a new block of memory is allocated on the stack for that local variables and parameters. When the function return, this block of memory is deallocated and stack's pointer moves back to function's previous block of memory. (Access by the function - scope)
- Fast allocate and deallocate duel to the fixed size.
- Stack overflow when too much data is allocated.
- For small, short-lived variables.
# Heap
- Not organized, can be accessed randomly. (access by any part of the program)
- Slower to allocate, when object is no longer needed, it must be deallocated (garbage collect).
- No fixed size, so the garbage collector must constantly monitor and manage the memory being used.
- For large, long-lived objects.
```c#
int x = 5; // x is a local variable and is stored on the stack  
  
// y is a reference to an object and is stored on the stack  
// the object itself is stored on the heap  
string y = new string("hello");  
  
// z is a reference to the same object as y  
// both y and z are stored on the stack, but the object is only stored once on the heap  
string z = y;  
  
// the object referenced by y and z is no longer needed, so the garbage collector will deallocate the memory on the heap  
y = null;  
z = null;
```