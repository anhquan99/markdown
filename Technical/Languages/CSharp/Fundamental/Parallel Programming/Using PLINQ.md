# Merge options
- Once the query completes, the result needs to be merged so that they can make available to the consuming thread.
- Types:
	- `NotBuffered`: The results of concurrent tasks are not buffered. As soon as any of the tasks finish, they return the result to the consuming thread
	- `AutoBuffered`: Depending on the size of the collection, multiple buffers might be returned. Using this option, the consuming thread needs to wait longer to get the first result. This is also the default option.
# Disadvantages
- **Parallel is not always faster:** Parallelization is an overhead. Unless your source collection is huge, or it has compute-bound operations, it makes more sense to execute the operations in sequence.
- **Avoid I/O operations that involve atomicity:** All I/O operations that involve writing to a filesystem, database, network, or shared memory location should be avoided inside PLINQ. This is because these methods are not thread-safe, so using them may lead to exceptions. A solution would be to use synchronization primitives, but this would also reduce performance drastically.
- **Your queries may not always be running in parallel:** Parallelization in PLINQ is a decision that's taken by CLR. Even if we called the AsParallel() method in the query, it isn't guaranteed to take a parallel path and may run sequentially instead.
# Factors that affect the performance of PLINQ (speedups)
- PLINQ performs best in *delightfully parallel* scenarios, where threads don't have to share a state and don't have to worry about the order of execution.
## Degree of parallelism
With a greater number of cores at our disposal, we can achieve significant performance gains since TPL makes sure multiple tasks can execute concurrently on multiple cores. This improvement in performance may not be exponential and, while tuning the performance, we should try to run on different systems with multiple cores and compare results.
## Merge option
We can significantly improve the user experience in scenarios where results change often and the user wants to see results as soon as possible without waiting.
## Partitioning type
We should always check whether our work items are balanced or unbalanced. For unbalanced work item scenarios, custom partitioners may be introduced to improve performance.
## Deciding when to stay sequential with PLINQ
- We should think about the cost of each operation, then make decision to stay sequential or move to parallelism. Parallel queries may not always be fast due to the additional overhead of partitioning, scheduling, and so on.
  $Computational Cost = Cost to execute 1 work item * total number of work items$
- Parallel queries can provide significant performance gains with increasing computational cost per item. However, if the performance gain is very low, it makes sense to execute the query sequentially.
## Order of operation
PLINQ provides better performance with unordered collections as there are performance costs associated with making collections execute as ordered.
## Forcing parallelism
Since PLINQ is not guaranteed to carry out parallel execution every time, we can control this using `WithExecutionMode` and set `ForceParallelism` to force parallelism.