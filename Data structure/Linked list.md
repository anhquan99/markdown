# Definition
- Linked list is a data structure which elements are not stored at contiguous memory locations.
# Why linked list
- Size of the array is fixed
- Insert/remove new element in an array is expensive
# Drawbacks
- Random access is not allowed.
- Extra memory space for a pointer
- Not cache friendly. Since linked list is not  contiguous memeory locations
# Singly Linked list
- ## Structure
	- `struct Node{`
		` int data;`
		`struct Node* next;`
		`}`
- ## Function
	-  Append node
		- At the end: travel to the end of the linked list and set last node pointer to new node and point new last node to null;
		- At specific position: travel to specific node  and point that pointer to new node. Point new node pointer to the node next to that specific node 
		- At the front: Point the new node pointer to the first pointer.
	- Delete node
		- Iterative method:
			1. Find the previous node of the node to be deleted
			2. Point the pointer of the previous node to the next node of the node to be deleted
			3. Free memory for the node to be deleted.
		- Recursive method:
			-  ![[Pasted image 20220309230710.png]]
	- Size() return number of the elements in list
	- Empty() check if list is empty
	- value_at(index) return value of the nth item
	- pop_front() remove front item and return its value
	- push_back() remove end item and return its value
	- front() get value of front item
	- back() get value of end item
	- insert(index, value) insert value at index
	- erase(index) remove node at given index
	- value_n_from)end(n) return the value of the node at nth position from the end of the list
	- reverse() reverse the list
	- remove_value(value)
# Circular linked list
- ## Structure
	- There is no null pointer, the last node will point to the header node
- ## Advantages
	- Any node can be starting point
	- Useful for implementation of [[Queue]]. In this case we don't  have to maintain 2 pointer for front and rear
	- Circular lists are useful in applications go in circular list.
	- Used for implementation of advanced data structure like [[Fibonacci Heap]]
## Doubly linked list
- ## Structure
	-  There are 4 pointers in each node:
		- Pointer points to current node from previous node.
		- Pointer points to previous node from current node.
		- Pointer points to next node from current node.
		- Pointer points to current node from next node.
		- ![[Pasted image 20220310123914.png]]
	- ## Advantages over singly linked list
		- Travel both from back and forward.
		- Delete operation is more efficient if pointer to the node to be deleted is given
		- Quickly insert a new node before a given node
	- ## Disadvantages
		- Extra space for more pointers.
		- All operations require an extra pointer previous to be maintained.