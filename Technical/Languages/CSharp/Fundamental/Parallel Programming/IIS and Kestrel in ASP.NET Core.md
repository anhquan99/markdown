# IIS threading model and internals
- At core of IIS lies the CLR thread pool.
- Every application that gets deployed to IIS is assigned a unique worker process. Each worker process has 2 thread pools: the worker thread pool and the IOCP (short for I/O completion port) thread pool:
	- Whenever we create a new thread pool thread using either legacy `ThreadPool.QueueUserWorkItem` or **TPL**, the ASP.NET runtime makes use of worker threads for processing.
	- Whenever we perform any I/O operations, that is, database calls, file read/write, or network calls to another web service, the ASP.NET runtime makes use of IOCP threads.
- By default, there is one worker thread and one IOCP thread per processor.
- If the number of available thread pool threads is less than the number of requests that are received by a server at any time, the requests will start to be queued. Later, the thread pool generates threads using one of two important algorithms, known as *Hill Climbing* and *Starvation Avoidance*.
## Starvation Avoidance
In this algorithm, `ThreadPool` keeps monitoring the queue, and if it doesn't progress, then it keeps pumping new threads into the queue.
## Hill Climbing
- `ThreadPool` tries to maximize the throughput using as few threads as possible.
# Kestrel
- Kestrel is open source and uses an event-driven, asynchronous I/O-based server. Kestrel is not a full-fledged web server and is recommended to be used behind full-featured web servers such as IIS and Nginx.
- Kestrel server is based on the builder pattern, and functionality can be added using the appropriate packages and extension methods.
# Introducing the best practices of threading in microservices
- Microservices can be stateless or stateful. The choice between stateless and stateful does have an impact on performance
	- Stateless: the requests can be served in any order without regard to what happened before or after the current request.
	- Stateful: all the requests should be processed in a particular order, like a queue.
- Microservices can be single-threaded or multithreaded as well, and this choice coupled with the state can really improve or degrade performance and should be well-thought-out while planning services.
## Design categorized
- **Single thread-single process:** The microservice runs on a single thread in a single CPU core. With every new request from a client, a new thread is created, which spawns a new process.
- **Single thread-multiple processes:** The microservice runs on a single thread but can spawn multiple processes, thereby improving their throughput.
- **Multiple threads-single process:** Microservice runs on multiple threads and there is a single long-lived process.
- **Asynchronous services:** We can avoid performance issues during integration with various application components by decoupling communication between microservices. Microservices must be created asynchronously by design to achieve this decoupling.
- **Dedicated thread pools:** If the application flow requires us to connect to various microservices, then it makes more sense to create a dedicated thread pool for such tasks.