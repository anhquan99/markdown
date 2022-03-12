# Definition
- Base binary tree data structure
- The left subtree of a node contains only nodes with keys lesser than the node's key
- The right subtree of a node contains only nodes with keys greater than the node's key
- The left and right subtree each must also be a binary search tree
- There must be no duplicate nodes
- Inorder traversal of BST always produces sorted output.
# Searching a key
1. Performed a binary search
2. Compare target value to node value if
	- Greater than current node value then move to the right node
	- Smaller than current node value then move to the left node
3. The search space will be reduced to n/2 make the time complexity is O($log(n)$).
# Insertion of a key
- A new key is always inserted at the leaf. We start searching a key from the root until we hit a leaf node. Once a leaf node is found, the new node is added as a child of the leaf node.
- The time complexity of this operation is $O(h)$ **where h is the height of the binary search tree**
# Delete node
- Node to be deleted is the leaf: Simply remove from the tree
- Node to be deleted has only one child: move the child to the parent node and delete
- Node to be deleted has 2 children: find inorder successor of the node. Copy contents of the inorder successor to the node and delete inorder succesor. Note that inorder predecessor can also used ![[Pasted image 20220311104228.png]]
# Function
- # Get node count
	- Get count of values stored
- # Print value
	- Prints the value in the tree, from min to max
- # Delete tree
- # Is in tree
	- Return true if given value exist in the tree
- # Get height
	- Return the height in nodes
- # Get min
	- Return min value in tree
- # Get max
	- Return max value in tree
- # Is binary search tree
- # Delete value
- # Get successor
	- Return next-highest value in tree after fgiven value, -1 if none