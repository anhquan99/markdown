# Definition
- A heap is a special Tree-based data structure in which the tree is a complete binary tree
- **Max-heap:** the key present at the root node must be greatest among the keys present at all of its children. The same property must be recursively true for all sub-trees in that Binary tree
- **Min-heap:** like **max-heap** but the root node must be the minimum among the keys present at all of its children.
- ![[Pasted image 20220311113953.png]]
# Functions
- **getMin**: returns the root elements of min heap. Time complexity $O(1)$
- **extractMin:** remove the minimum element from MinHeap. Time complexity is $O(log(n))$ 
- **decreaseKey:** Decrease value of key. The time complexity of this operations is $O(log(n))$. If the decreases key value of a node is greater than the parent of the node, then we don’t need to do anything. Otherwise, we need to traverse up to fix the violated heap property.
- **insert:** Inserting a new key takes $O(Logn)$ time. We add a new key at the end of the tree. IF new key is greater than its parent, then we don’t need to do anything. Otherwise, we need to traverse up to fix the violated heap property.
- **delete:** Deleting a key also takes $O(Logn)$ time. We replace the key to be deleted with minum infinite by calling decreaseKey(). After decreaseKey(), the minus infinite value must reach root, so we call extractMin() to remove the key.
# Application
- [[Heap sort]]
- [[Priority Queue]]
- [[Binary heap]]
- [[Prims's algorithms 1]]
- [[Dijkstra's algorithms 1]]
# Variations
- [[Binomoial heap]]
- [[Fibonacco heap]]