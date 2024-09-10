- A thread is the smallest unit of processing (CPU utilization) that can be scheduled and executed by the operating system.
- Threads are components of a process, which contains the program code and its activity
![[Pasted image 20240904165955.png]]
- Registers: each thread uses registers set to keep track of its state.
- Stack: each thread has its own stack, which is used for function calls and local variables.
- Shared resources: threads share the same code, data, and files, allowing them to communicate and share information more efficiently than separate processes.
- This structure allows multiple threads within the same process to execute concurrently, improving the performance and responsiveness of applications, especially on multi-core processors.