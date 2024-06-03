# Definition
- A heap is a special Tree-based data structure in which the tree is a complete binary tree
- **Max-heap:** the key present at the root node must be greatest among the keys present at all of its children. The same property must be recursively true for all sub-trees in that Binary tree
- **Min-heap:** like **max-heap**, but the root node must be the minimum among the keys present at all of its children.
- ![[Pasted image 20220311113953.png]]
# Functions
- **getMin**: returns the root elements of min heap. Time complexity $O(1)$
- **extractMin:** remove the minimum element from MinHeap. Time complexity is $O(log(n))$ 
- **decreaseKey:** Decrease value of key. The time complexity of this operation is $O(log(n))$. If the decreases key value of a node is greater than the parent of the node, then we don’t need to do anything. Otherwise, we need to traverse up to fix the violated heap property.
- **insert:** Inserting a new key takes $O(Logn)$ time. We add a new key at the end of the tree. IF new key is greater than its parent, then we don’t need to do anything. Otherwise, we need to traverse up to fix the violated heap property.
- **delete:** Deleting a key also takes $O(Logn)$ time. We replace the key to be deleted with minimum infinite by calling decreaseKey(). After decreaseKey(), the minus infinite value must reach root, so we call extractMin() to remove the key.
# Properties
- Complete binary tree or k-heap (nodes have k children):
	-  The number in each node is the key, not value. Keys are used to sort the nodes/construct the tree, and values are the data we want the heap to store.
	- Unlike binary search tree, there is no comparable relationship between children, in the same it is not comparable but in the different level it is comparable
- The minimum or maximum always at the root of the tree lead to the constant-time access.
# Implementation
- Being a complete tree makes an **array** a natural choice to implement a heap since it can be stored compactly and no space wasted.
  ![[Pasted image 20240526221631.png]]
- For node *i*, its children are stored at *2i+1* and *2i+2*, and its parent is at *floor((i-1)/2)*.
- If implement **k-heap** then the children are at *ki+1* to *ki+k*, and its parent is at *floor((i-1)/k)*.
# Application
- [[Heap sort]]
- [[Priority Queue]]
- [[Binary heap]]
- [[Prims's algorithms]]
- [[Dijkstra's algorithms]]
# Variations
- [[Binomoial heap]]
- [[Fibonacco heap]]