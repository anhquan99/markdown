# Definition
- Trees are a hierarchical data structure.
- The topmost node is called the root of the tree. The elements that are under an element are called its children. The element directly above something is called its parent.
# Why trees
- Tree help store information forms a hierarchy like file system,...
- Provide moderate access/ search (quicker than linked list and slower than arrays)
- Trees don't have upper limit on number of nodes as node using pointer.
# Application
- Manipulate hierarchical data
- Make information easy to search
- Manipulate sorted lists of data
- Router algorithms
- Form of a multi-stage decision-making
- As a workflow for compositing digital images for visual effects.
# Structure
`struct node{`
`  data_type dataName;`
`  struct node* left;`
`  struct node* right;`
`}`
# Properties
- The maximum number of nodes at level L is $2^L$
- The maximum number of nodes in a binary tree of height H is $2^H - 1$
- In a Binary Tree with N nodes, the minimum possible height or the minimum number of levels is $Log2(N+1)$.
-  A Binary Tree with L leaves has at least $| Log2L |+ 1$  levels.
- In a Binary tree where every node has 0 or 2 children, the number of leaf nodes is always one more than nodes with two children.
# Functions 
- Traversal
	- Inorder (Left, Root, Right) ![[Pasted image 20220311080945.png]]
	- Preorder (Root, Left, Right) ![[Pasted image 20220311080951.png]]
	- Postorder (Left, Right, Root) ![[Pasted image 20220311080917.png]]
	- ![[Pasted image 20220311080856.png]]
	- Using [[Stack]]
		1. Create an empty stack
		2. Initialize current node as root
		3. Push the current node to stack and set current = current->left until current is NULL
		4. If current is NULL and stack is not empty
			- Pop the top item from stack
			- Print the popped item, set current = popped_item->right
			- Go back to step 3
		5. If current is NULL and stack is empty then the traversal is NULL
	- [Moriris traversal](https://www.educative.io/edpresso/what-is-morris-traversal)
# Variations
- [[Binary tree]]
	- **Full Binary tree:** every node has 0 or 2 children.
	              18
	           /       \  
	         15         30  
	        /  \        /  \
	      40    50    100   40	
	             18
	           /    \   
	         15     20    
	        /  \       
	      40    50   
	    /   \
	   30   50	
	               18
	            /     \  
	          40       30  
	                   /  \
	                 100   40
	- **Complete Binary tree:** if all the levels are completely filled except possibly the last level and the last level has all keys as left as possible
			  18
           /       \  
         15         30  
        /  \        /  \
      40    50    100   40
               18
           /       \  
         15         30  
        /  \        /  \
      40    50    100   40
     /  \   /
    8   7  9
	- Perfect Binary tree: When all the internal nodes have 2 children and all leaf nodes are at the same level
			 18
           /       \  
         15         30  
        /  \        /  \
      40    50    100   40
               18
           /       \  
         15         30
	- Balance Binary tree: A binary tree is balanced if the height of the tree is O(Log n) where n is the number of nodes.
- [[Binary search tree]]
- [[Red black tree]]