# Definition
- Queue is a linear structure which follow FIFO (First in first out). 
# Usage
- When a resource is shared among multiple comsumers
- When data is transferred asynchronously
- In Operation systems:
	- Semaphores
	- FCFS (First come first serve)
	- Buffer for devices like keyboard
- In networks:
	- Queues in routers/ swiches
	- Mail Queues
# Circular buffer
- Using definded array to make queue.
- Buffer can be full when all element in array is not empty
- Use front pointer and tail pointer to index position
# Function
- ## Using linked list
	- enqueue(value) add value at position tail
	- dequeue() remove value at position head
	- empty()
- ## Fixed array:
	- enqueue(value) add item at the end of available storage
	- dequeue()
	- empty()
	- full()
# Cost
- Bad implementation using linked where you enqueue at head and dequeue at tail would be O(n) because you'd need the next to last element, causing a full travelsal each dequeue
- Enqueueu, dequeue, empty: O(1) 
# Variation
- [[Priority Queue]]
# LRU (Least recently used) Cache implementation
- ![[Pasted image 20220310200011.png ]]