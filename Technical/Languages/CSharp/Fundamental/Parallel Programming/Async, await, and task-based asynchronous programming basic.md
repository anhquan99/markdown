- Async methods work in a chain. Once you have made any method asynchronous, then all methods that call that method need to be converted to being asynchronous as well, thus creating a long chain of asynchronous methods.
# Return type of async methods
- `void`
- `Task`
- `Task<T>`
# Exception handling with async code
- Scenarios:
	- **Calling the async method from outside the try-catch block without the `await` keyword:** the task `Status` will be `Faulted`.
	- **Calling the async method from inside the try-catch block without the `await` keyword:** the task `Status` will be `RanToCompletion`.
	- **Calling the async method with the `await` keyword from outside the try-catch block:** the task `Status` will be `Faulted`.
	- **Methods returning `void`:** the program will crash. It makes sense to never return `void` from async methods. We should write code so that it never crashes or only crash gracefully after logging exceptions.
# `SynchronizationContext`
- Since new async APIs work with the `Task` class, all the calls are executed by the `ThreadPool` thread. When we make async calls - say, to fetch data from a network - the control gets transferred to the I/O completion port thread, which is managed by the OS. Usually, this is only one thread that is shared across all network requests. When the I/O request completes, the OS fires an interrupt signal that adds a job to the queue of the I/O completion port. In the case of server-side applications, which usually work in **Multi-Threaded Apartment (MTA)** mode, any thread can start an async request and any other thread can receive it.
- In the case of Windows applications, however, (including both WinForms and WPF), which work in **Single-Threaded Apartment (STA)** mode, it becomes important that an async call gets returned to the same thread that started it (normally a UI thread). Every UI thread in a Windows application has a SynchronizationContext that makes sure that the code is always executed by the correct thread. This is important due to control ownership. To avoid cross-threading issues, only the owner thread can change the values of the controls. The most important method of the `SynchronizationContext` class is Post, which can make a delegate run in the right context, thus avoiding cross-threading issues.
- Whenever we await a task, the current `SynchronizationContext` is captured. Then, when the method needs to be resumed, the await keyword internally uses the Post method to resume the method in the captured `SynchronizationContext`. Calling the Post method is very costly, however, but there is a built-in performance optimization provided by the framework. The Post method doesn't get called if the captured `SynchronizationContext` is the same as the current `SynchronizationContext` of the returning thread.
# Parallelism vs async await
```c#
// async await code
using System.Diagnostics;

MainAsync(args).GetAwaiter().GetResult();
Console.ReadKey();

static async Task MainAsync(string[] args)
{
    var sw = Stopwatch.StartNew();
    var val1 = await Task1();
    var val2 = await Task2();
    var val3 = await Task3();
    sw.Stop();
    Console.WriteLine($"Total time: {sw.ElapsedMilliseconds}");
}
static async Task<int> Task1()
{
    await Task.Delay(1000);
    return 100;
}
static async Task<int> Task2()
{
    await Task.Delay(1000);
    return 200;
}
static async Task<int> Task3()
{
    await Task.Delay(1000);
    return 300;
}
// parallelism code
using System.Diagnostics;

MainAsync(args).GetAwaiter().GetResult();
Console.ReadKey();

static async Task MainAsync(string[] args)
{
    var sw = Stopwatch.StartNew();
    await Task.WhenAll(Task1(), Task2(), Task3());
    sw.Stop();
    Console.WriteLine($"Total time: {sw.ElapsedMilliseconds}");
}
static async Task<int> Task1()
{
    await Task.Delay(1000);
    return 100;
}
static async Task<int> Task2()
{
    await Task.Delay(1000);
    return 200;
}
static async Task<int> Task3()
{
    await Task.Delay(1000);
    return 300;
}
```
- In the code example above the async await code execute synchronously while the parallelism execute in parallel, this give the parallelism code more performance.
- The async await code awaits each task sequentially, you're essentially creating a chain where each task must wait for its predecessor to complete, leading to a cumulative execution time.
- This example shows that async await does not guarantee parallelism.
# Guidelines for using async code
## Avoid using async avoid.
## Async chain all the way
```C#
private async Task DelayAsync() 
{ 
	await Task.Delay(2000); 
} 
public void Deadlock() 
{ 
	var task = DelayAsync(); task.Wait(); 
}
```
- If we call the Deadlock() method from any ASP.NET or GUI-based application, it would create a deadlock, although the same code would run fine in a console application. When we call the DelayAsync() method, it captures the current SynchronizationContext, or the current TaskScheduler if the SynchronizationContext is null. When the awaited task is complete, it tries to execute the remainder of the method with the captured context. The problem here is that there is already a thread that's waiting synchronously for the async method to finish. In this situation, both threads will be waiting for the other thread to finish, thus causing a deadlock. This problem is raised only in GUI-based or ASP.NET applications because they rely on the SynchronizationContext that can only execute one chunk of code at a time. Console applications, on the other hand, utilize ThreadPool instead of SynchronizationContext. When the await finishes, the pending async method part is scheduled on a ThreadPool thread. The method is completed on a separate thread and returns the task back to the caller, so there is no deadlock.
## Using `ConfigureAwait` wherever possible
# Notes
- Never try to create sample `async/await` code in a console application and copy and paste it in a GUI or ASP.NET application, as they have different models for executing async code.