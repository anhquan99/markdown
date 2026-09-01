- The `IProducerConsumerCollection<T>` interface that represents thread-safe stacks, queues, and bags.
# `ConcurrentQueue<T>`
- Concurrent queue can be used to solve producer/consumer scenarios.
- In the producer/consumer. 1 or more thread produce data and or more threads consume data, this lead to race conditions among threads. Classified:
	- **Pure producer-consumer:** where a thread can either only produce data or only consume data but cannot do both.
	- **Mixed producer-consumer:** where any thread can produce or consume data at the same time.
## Performance consideration
- `ConcurrentQueue` benefits over `queues` in cases:
	- Pure producer/consumer, where the processing time for each item is very low.
	- Pure producer/consumer, where only 1 dedicated producer thread and only 1 dedicated consumer thread.
	- Pure producer/consumer, where processing time is 500 **FLOPS** (short for **Floating-Point Operations Per Second**) or more.
# `BlockingCollection<T>`
- We can add or remove items from the collection concurrently without worrying about synchronization, which is handled automatically.
- There will be 2 threads
	- Producer: produces data, we can limit the maximum number of items that can be produces before it enter sleep mode and is then blocker.
	- Consumer: consumes data, will be blocked when the collection is emptied and is unblocked when the producer thread adds some data to the collection.
- Aspects:
	- **Bounding:** bound the limit of the producer thread and make it enters sleep mode.
	- **Blocking:** block the consumer thread when the collection is empty.