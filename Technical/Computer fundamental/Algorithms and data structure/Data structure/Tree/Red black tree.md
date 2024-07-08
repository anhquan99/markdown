- Red black tree is a type of balanced binary search tree.
# Idea
- Most of the binary search tree (BST) operations (search, max, min, insert, ...) take `O(h)` time where h is the height of the BST, and it may become `O(n)` for a skewed binary tree. If tree can maintain the balance `O(log n)` after every insertion and deletion, then it can guarantee the upper bound of the `O(log n)` for all these operations.
- Red black tree is more preferred when the data is frequently insert and delete because of the rotations, else the [[AVL tree]] is more preferred.
# Properties
1. **Red/Black Property:** Every node is colored, either red or black.
2. **Root Property:** The root is black.
3. **Leaf Property:** Every leaf (NULL) is black.
4. **Red Property:** If a red node has children, then, the children are always black.
5. **Depth Property:** For each node, any simple path from this node to any of its descendant leaf has the same black-depth (the number of black nodes).
   ![[Pasted image 20240707190754.png]]
# Node attributes
- Color
- Left child
- Right child
- Parent
# Operations
## Rotating the subtrees
- Used for maintaining the properties of a red black tree when they are violated by other operations.
### Left rotate
- In left-rotation, the arrangement of the nodes on the right is transformed into the arrangements on the left node.
- Steps:
	1. If `y` node has left subtree, then assign `x` as the parent node of the left subtree of `y`.
	2. If `x` does not have parent, make `y` as the root of the tree, else assign `y` as the child of `x` parent.
```
Before Rotation:

    x                                              
     \                                             
      y                                                         
     / \                                                     
    a   b                                                     

After Left Rotation:

      y
     / \
    x   b
   /
  a
```
### Right rotate
- In right-rotation, the arrangement of the nodes on the left is transformed into the arrangements on the right node.
- Steps:
	1. if `y` has right subtree, then assign `x` as the parent of the right subtree of `y`.
	2. If `x` does not have parent, make `y` as the root of the tree, else assign `y` as the child of the `x` parent.
```
Befor Right Rotation:    

      x
     /
    y
   / \
  a   b

After Right Rotation:

    y
   / \
  a   x
     /
    b
```
## Insertion
- While inserting a new node, the new node is always inserted as red node. After insertion of a new node, if the tree is violating the properties of the red-black tree then do the following operations:
	- Recolor
	- Rotation
- Maintain red black property after insertion
	- **Case 1:** uncle is red: recolor the parent and uncle to black, and the grandparent to red. Then move up the tree to check for further violations.
	- **Case 2:** uncle is black:
		- **Sub-case 2.1:** if node is the right child, then perform a left rotation on the parent.
		- **Sub-case 2.2:** if node is the left child, then perform a right rotation on the grandparent and recolor.
## Search 
- Same as binary search tree.
## Deletion
- Steps:
	1. BST deletion.
	2. Fix double black: if the black node is deleted, a *double black* condition might arise, which requires specific fixes.
- Fix violations during deletion
	- **Case 1:** sibling is red: rotate the parent of and recolor the sibling and parent.
	- **Case 2:** sibling is black:
		- **Case 2.1:** sibling's children are black: recolor the sibling and propagate the double black upwards.
		- **Case 2.2:** at least one of the sibling's children is red:
			- If the sibling's far child is red: perform a rotation on the parent and sibling, and recolor appropriately.
			- If the sibling's near child is red: rotate the sibling and its child, then handle as above.
# Avantages
- Self-balancing.
- Efficient search, insertion and delete.
- Widely used.
# Disadvantages
- Constant overhead.
- Not optimal for all use cases.
# Application
- Implementing maps and sets.
- Priority queues.
- File systems.
- In-memory databases.