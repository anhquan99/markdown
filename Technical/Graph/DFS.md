# Idea
- Search into the depth rather the breadth.
- DFS find the first vertex and continue find next vertex in founded vertex, keep repeat until no more vertex is found.
 # Time complexity
- $O(n^2)$ in case graph representative is adjacency matrix
- $O(n.m)$ in case graph representative is edges list
- $O(max(n,m))$ in case graph representative is adjacency list
# Tree traversal
- Inorder: left to right.
- Preorder: root → left → right.
- Postorder: right to left.
# Coding
- Recursive ![[Pasted image 20220311184544.png]]
- Non-recursive ![[Pasted image 20220311184611.png]]