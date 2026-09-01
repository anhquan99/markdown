# When to use asynchronous programming
The processing Direct Memory Access (DMA) is done by CPU, where the calling thread makes a call to the I/O API and waits for the task to complete by moving to a block state.
# Asynchronous code in `C#`
- `Delete.BeginInvoke()` - no longer support in .NET Core
- `Task` class
- `IAsyncResult` interface - no longer support in .NET Core
- `async` and `await` keywords
# When not to use asynchronous programming
- In a single database without connection polling.
- When it is important that the code is easy to read and maintain.
- For simple and short-running operations.
- For applications with lots of shared resources: we need to apply synchronization.
# Example use cases
- Logging and auditing.
- Service call.
- Creating responsive UIs.
- CPU-bound applications
# Asynchronous patterns
- Asynchronous programming model (APM)
- Event-based asynchronous pattern (EAP)