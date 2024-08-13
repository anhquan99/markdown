# Process
- A process is an address space in the memory, where windows, web, or mobile application need it to run.
- Process protect program against other programs that run on the same system from accessing data by another, provide isolation to run independently.
# Multitasking
- With multicore processes, the computer can run number of tasks (equal to number of cores) at a time.
# Hyper-threading
- A technology that enabled single-processor chips to run 2 virtual (logical) cores and capable of executing 2 tasks at a time.
# Flynn's taxonomy
- **Single Instruction, Single Data (SISD):** In this model, there is a single control unit and a single instruction stream. These systems can only execute one instruction at a time without any parallel processing. All single-core processor machines are based on the SISD architecture.
- **Single Instruction, Multiple Data (SIMD):** In this model, we have a single instruction stream and multiple data streams. The same instruction stream is applied to multiple data streams in parallel. This is handy in speculativeapproach scenarios where we have multiple algorithms for data, and we don't know which one will be faster. It provides the same input to all the algorithms and runs them in parallel on multiple processors.
- **Multiple Instructions, Single Data (MISD):** In this model, multiple instructions operate on one data stream. Therefore, multiple operations can be applied in parallel on the same data source. This is generally used for fault tolerance and in space shuttle flight control computers.
- **Multiple Instructions, Multiple Data (MIMD):** In this model, as the name suggests, we have multiple instruction streams and multiple data streams. Due to this, we can achieve true parallelism, where each processor can run different instructions on different data streams. Nowadays, this architecture is used by most computer systems.
# Threads
- A thread is a unit of execution inside a process.
## Apartment state
- Another important aspect of threads to understand is the apartment state. This is the area inside a thread where Component Object Model (COM) objects reside.
- A thread can belong to one of two apartment states:
	- **Single-Threaded Apartment (STA):** The underlying COM object can be accessed via a single thread only.
	- **Multi-Threaded Apartment (MTA):** The underlying COM object can be accessed via multiple threads at a time.
## Types
- Foreground threads: direct impact on an application's lifetime. The application keeps running until there is a foreground thread.
- Background threads: no impact on the application's lifetime. When the application exists, all background threads are killed.
## Multithreading
- Parallel execution of code in .NET is achieved through multithreading. A process (or application) can utilize any number of threads, depending on its hardware capabilities.
- Multithreading typically functions using a scheduling component known as a thread scheduler, which keeps track of when a thread should run out of active threads inside a process.
- Every thread that runs inside a process is assigned a time slice by the OS based on the thread priority scheduling algorithm. Every OS can have a different scheduling algorithm for running threads, so the order of execution may vary in different operating systems. This makes it more difficult to troubleshoot threading errors. The most common scheduling algorithm is as follows:
	1. Find the threads with the highest priority and schedule them to run.
	2. If there is more than one thread with the highest priority, each thread is assigned a fixed time slices in which they can execute.
	3. Once the highest-priority threads finish executing, the lower-priority threads start to be allocated to time slices in which it can begin executing.
	4. If a new highest-priority thread is created, low-priority threads are pushed back again.
- Time slicing refers to switching the execution between the active threads. It can vary, depending on the hardware configuration. A single-core processor machine can only run one thread at a time, so the thread scheduler carries out the time slicing. The time slice largely depends on the clock speed of the CPU, but there still aren't many performance gains that can be achieved via multithreading in such systems. Moreover, context switching comes with performance overheads. If the work that's allocated to a thread spans multiple time slices, then the thread needs to be switched in and out of memory. Every time it switches out, it needs to bundle and save its state (data) and reload it when it switches back in.
- **Concurrency** is a concept that's primarily used in the context of multicore processors. A multicore processor has a higher number of CPUs available, as we discussed previously, and therefore different threads can be run simultaneously on different CPUs. A higher number of processors means a higher degree of concurrency.
## Advantages
- Threads can be utilized to free up the main thread.
- Thread can be used to break up a task into smaller units that can be executed concurrently.
## Disadvantages
- With more threads, the code becomes difficult to debug and maintain. 
- Thread creation puts a load on the system in terms of memory and CPU resources. 
- We need to do exception handling inside the worker method as any unhandled exceptions can result in the program crashing.
# `ThreadPool`
- Thread creation is an expensive operation in terms of both memory and CPU resources.
- Create large number of threads can sometimes decrease application performance, you should aim to create an optimal number of threads.
- Because every program gets a time slice by the CPU, which is then distributed among the threads inside the applicationn. If you create too many threads, they may not be able to do any constructive work before being swapped out of memory to give the time slice other similar-priority threads.
- Depending on the system, the optimal number of threads can be different. Rather than finding the optimal number of threads, there is Common Language Runtime (CLR) which has an algorithm to determine the optimal number based on the CPU load at any point in time. It maintains a pool of threads, known as the `ThreadPool`.
- The `ThreadPool` resides in a process and each application has its own pool of threads and assigns them to a task. When the work is finished, the threads are returned to the pool, where they can be assigned to the next work item, thereby preventing the cost of creating and destroying threads.
## Advantages
- Threads can be utilized to free up the main thread.
- Threads are created and maintained in an optimal way by CLR.
## Disadvantages
- With more threads, the code becomes difficult to debug and maintain.
- We need to do exception handling inside the worker method as any unhandled exception can result in the program crashing.
- Progress reporting, cancellations, and completion logic need to be written from scratch.
## Avoid when
- When we need a foreground thread. 
- When we need to set an explicit priority to a thread. 
- When we have long-running or blocking tasks. Having numerous blocked threads in the pool will prevent new tasks from starting due to the limited number of threads that are available per process in ThreadPool. 
- If we need to dedicate a thread to a task by providing it a distinct identity since we cannot name a `ThreadPool` thread.
# `BackgroundWorker`
- `BackgroundWorker` is a construct provided by .NET to create more manageable threads from a `ThreadPool`.
- When doing the I/O operations, the execution is passed from the thread to the CPU to perform the task. When it is complete, the result of the operation is returned to the caller thread. This passing and returning action make the application become unresponsive, it makes sense to delegate these tasks to the work thread.
- `BackgroundWorker` can be used to create worker thread utilizing `ThreadPool`.
## Advantages
- Threads can be utilized to free up the main thread. 
- Threads are created and maintained in an optimal way by the ThreadPool class's CLR. 
- Graceful and automatic exception handling. Supports progress reporting, cancellation, and completion logic using events.
## Disadvantages
- With more threads, the code becomes more difficult to maintain and debug.
# Parallel programming
## Advantages
- Enhanced performance
- Improved GUI responsiveness
- The simultaneous and parallelized occurrence of tasks
## Disadvantages
- Complex debugging and testing processes
- Context switching overheads
- High chance of deadlock occurrence
- Difficult to program
- Unpredictable results