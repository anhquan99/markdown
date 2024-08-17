- Synchronization primitives are simple software mechanisms that are provided by the underlying platform (the OS). They help in multithreading the kernel. Synchronization primitives internally use low-level atomic operations, as well as memory barriers. This means that users of synchronization primitives don't have to worry about implementing locks and memory barriers themselves. Some common examples of synchronization primitives are locks, mutexes, conditional variables, and semaphores. The monitor is a higher-level synchronization tool that makes use of other synchronization primitives internally.
# Critical section
- Critical section is part of the execution path of a thread that must be protected from concurrent access in order to maintain some invariants. Critical section is not a synchronization primitive in itself but relies on synchronization primitives.
# Categories
## Interlocked operations
- The interlocked class encapsulates synchronization primitives and is used to provide atomic operations to variables that are shared across threads.
- On singlecore processors, only one thread gets a CPU slice while other threads wait for their turn. This ensures that whenever a thread accesses the memory (for loading and storing), it is in the right order. This model is also known as a **sequential consistency model**. In the case of multicore processor systems, multiple threads run concurrently. Sequential consistency is not guaranteed in these systems since either the hardware or the **Just in Time (JIT)** compiler might reorder the memory instructions to improve performance. The memory instructions may also be reordered for performance purposes for caching, load speculations, or delaying store operations.
### What is reordering?
- For a given sequence of code statements, the compiler can choose to either execute them in the same order as they are received or reorder them to gain performance if multiple threads are working on the same code.
```C#
a = b;
c = 1;
// can be change to this
c = 1;
a = b;
```
### Types of memory barriers
Memory barriers ensure that any code statements above or below the barrier will not cross the barrier, thereby enforcing the order of the code.
- Store (write) memory barrier: A store memory barrier ensures that no store operations are allowed to move across the barrier
- Load (read) memory barrier: A load barrier ensures that no load operations are allowed to move across the barrier but places no such enforcement on store operations.
- Full memory barrier: A full memory barrier ensures ordering by not allowing store or load operations to move across the memory barrier.
### How locking works
1. A thread or group of threads access a shared resource by acquiring a lock.
2. Other threads that cannot get access to a lock go into a wait state.
3. As soon as the lock is freed by one of the threads, it is acquired by another thread, which starts its execution.
### Thread state
- `Unstarted`: thread has been created by CLR but has not been yet.
- `Running`
- `WaitSleepJoin`: thread is in a blocked state as a result of invoking named methods. (Once blocked, the thread immediately yields the CPU time slice and doesn't use the processor slice until the blocked condition is satisfied. At this point, the thread is unblocked. Blocking and unblocking constitutes a performance overhead as this requires the CPU to carry out context switching)
- `StopRequested`: has been requested to stop.
- `Stopped`
- `AbortRequested`
- `Aborted`
- `SuspendRequested`
- `Suspended`
- `Background`: thread is being executed in the background.
   ![[Pasted image 20240814161155.png]]
# Lock, mutex, and semaphore
- Lock and mutex are lock constructs that allow only 1 thread to access a protected resource. Lock is a shortcut implementation that uses another higher-level sync class called `Monitor`.
	- Lock only allow 1 thread per process to access the resource.
	- Mutex allows only 1 thread to access a protected resource but can work across processes as well.
- Semaphore is a locking construct that allow a specified number of threads to access a protected resource. Lock can only synchronize access inside a process, but if we need to access a system-level resource or shared memory, we need to actually synchronize access across multiple processes. A mutex allows us to synchronize access to resources across processes by providing a kernel-level lock
- Type of semaphore:
	- Local semaphore: local to the application, created without name.
	- Global semaphore: global to the operating system as it applies kernel or system-level locking primitives. Created with a name.
# Signaling primitives
While creating tasks, you may come across a producer/consumer scenario where a thread (the consumer) is waiting for a shared resource to be updated by another thread (the producer). Since the consumer doesn't know when the producer is going to update the shared resource, it keeps on polling the shared resource, which can lead to race conditions. Polling is highly inefficient in dealing with these scenarios. It is better to use the signaling primitives that are provided by the .NET Framework. With signaling primitives, the consumer thread is paused until it receives a signal from the producer thread.