# Definition
- Priority Queue is an abstract data type, which similar to a [[Queue]]. In the priority queue, every element has some priority. The priority of the elements in a priority queue determines the order in which elements are removed from the priority queue. Therefore all the elements are either arranged in an ascending or descending order.
- Every item has a priority associate with it.
- An element with high priority is dequeued before an element with low priority
- If two elements have the same priority, they are served according to their order in the queue.
# Function
- Insertion: compare the element until the queue is in correct order
- Deletion: Remove the maximum priority element.
- Peek: return maximum element from Max Heap or minimum element from Min Heap withou deleting the node from priority queue
# Type of Priority Queue:
- Ascending order
- Descending order
# Implement Priority Queue
- ## [[Array]]
Arrays | enqueue() | dequeue() | peek()
------------ | ------------| ------------| ------------
Time complexity | O(1) | O(n) | O(n)
- ## [[Linked list]]
Linked list | push() | pop() | peek()
------------ | ------------| ------------| ------------
Time complexity | O(n) | O(1) | O(1)
- [[Heap]] 
	- insert
	- extractMax
	- remove
	- getMax
	- changePriority
Heap | insert() | remove() | peek()
------------ | ------------| ------------| ------------
Time complexity | O(logn) | O(logn) | O(1)
- [[Binary tree]]
	- 
Binary tree | enqueue() | dequeue() | peek()
------------ | ------------| ------------| ------------
Time complexity | O(1) | O(logn) | O(logn)