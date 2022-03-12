# Idea
- Search into the breadth.
- BFS finds all connected vertex in current vertex and add to queue.
- After all vertexes had been found on current vertex. BFS dequeue to get next the vertex then repeat find all connected vertex,... Until queue is empty, BFS is done.  
 # Time complexity
 - n is the vertexs of the graph
 - m is the edges of the graph
- $O(n^2)$ in case graph representative is adjacency matrix
- $O(n.m)$ in case graph representative is edges list
- $O(max(n,m))$ in case graph representative is adjacency list
# Coding
- ![[Pasted image 20220311185207.png]]