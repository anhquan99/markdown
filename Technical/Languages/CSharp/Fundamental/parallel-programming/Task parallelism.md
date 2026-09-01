# Tasks
- A task is a wrapper over a thread, which is created via `ThreadPool`.
# How to cancel tasks
- .NET provide 2 classes to support task cancellation:
	- `CancellationTokenSource`: responsible for creating cancelling tokens and passing the cancellation request to all the token that were created via the source.
	- `CancellationToken`: is used by listeners to monitor the current state of a request.
# Work-stealing queues
- Work-stealing is a performance optimization technique for a thread pool. Every thread pool maintains a single global queue of tasks that are created inside a process.
- The .NET Framework works around this performance loss by introducing the concept of local queues, which are managed by threads. Each thread has access to a global queue and also maintains its own thread-local queue to store work items in. Parent tasks can be scheduled inside the global queue. When tasks execute and need to create subtasks, they can be stacked up on local queues and processed using the FIFO algorithm as soon as the thread finishes executing.
- Example: 
	1. **Task 1, task 2** are generated from the main thread, these tasks are queued to the global queue to be executed later based on the availability of the thread inside the thread pool. **Each task will go to thread with the same number.**
	2. If **task 1 and task 2** generate more tasks, the new task will be stored in the thread-local queue. 
	3. Once the thread 1 finished with task 1, it will look into its local queue and pick up the last task (LIFO). This make the last task may still be cache and so it doesn't need to be reloaded.
	- Once thread 1 finish all task in its local queue it will search the global queue, if there are no items in the global queue, it will search in the local queues in other threads but this time it will not take the last task from other thread to preserve other thread cache.
# `ValueTask`
- `ValueTask` is a type in .NET that represent an async operation that may or may not be completed async.
- More lightweight alternative to Task when the operation might complete sync, avoiding the overhead of creating a Task object.
- Use `ValueTask` instead of Task when the operation might complete sync. This avoids the overhead of creating a Task object when not needed.
- Always use await when calling a `ValueTask` returning method. This ensures that the async operation is properly awaited, even if it completes sync.
- Beware of the potential for sync completion.
# Note
- Thread is not task and thread is more expensive than task.
- `await` signals the program at that point, you're waiting for an async operation to complete, the current thread will pause execute which allows other tasks is executed and the code block after the `await` will be queued to be executed (it will not executed immediately).