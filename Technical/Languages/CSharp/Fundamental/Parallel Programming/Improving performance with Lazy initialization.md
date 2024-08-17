# Lazy initialization
- Lazy loading is a commonly used design pattern in application programming wherein we defer the creation of an object until it is actually required in an application. Proper use of the lazy load pattern can significantly improve the performance of the application.
# `System.Lazy<T>`
- Provides benefits of lazy initialization without the need to worry about synchronization overheads.
- Objects created using `System.Lazy` are deferred until they are accessed for the first time.
- Implement lazy initialization:
	- Construction logic encapsulated inside a constructor.
	- Construction logic passed as a delegate to `Lazy<T>`.
# Handle exceptions with lazy pattern
- Lazy objects are immutable by design. This means that they always return the same instance that they were initialized with.
- Handle exceptions:
	- **No exceptions occur during initialization:** initialization logic runs once and the object is cached to be returned with subsequent access.
	- **Random exception while initialization with exception caching:** since the underlying object is not created, the initialization logic will run on every call. This is helpful in scenarios where the construction logic depends on external factors such as an internet connection while calling the external service. If the internet goes down momentarily, then the initialization call will fail, but subsequent calls can return the data. By default, `Lazy<T>` will cache exceptions for all parameterized constructor implementations, but it will not cache exceptions for the parameter less constructor implementation.
	- **Not caching exception:** allow initialization logic to be run multiple times by different threads until 1 of the threads succeeds in running the initialization without any errors.
# Lazy initialization with thread-local storage
- In multithreaded programming, we often want to create a variable that is local to a thread, which means that each thread will have its own copy of the data. This holds true for all local variables, but global variables are always shared across threads.
- `Lazy<T>` vs `ThreadLocal<T>`:
	- Each thread initializes the `ThreadLocal<T>` variable using its own private data whereas, in the case of `Lazy<T>`, the initialization logic only runs once.
	- Unlike `Lazy<T>`, the `Value` property in `ThreadLocal<T>` is read/write.
	- In the absence of any initialization logic, the default value of `T` will be assigned to the `ThreadLocal<T>` variable.
# Reducing the overhead with lazy initializations
- `Lazy<T>` uses a level of indirection by wrapping the underlying object. This can cause computational as well as memory issues. To avoid wrapping objects, we can use the static variant of `Lazy<T>` class, which is the `LazyInitializer` class