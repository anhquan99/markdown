- Trie also known as a prefix tree, is a tree-like data structure used for efficient retrieval of key-value pairs.
- The word Trie is derived from reTREval.
# Idea
- Using array of pointer to store the tree and when querying start from the root trace back to the node by check the element exist on the tree.
  ![[Pasted image 20240611221147.png]]
# Usage
- Storing and retrieval of data and operations is the same as hash table.
- Advantages of trie over hash table:
	- Efficiently do prefix search (or auto-complete).
	- Searching for string in the large collection of strings in a trie can be done in `O(L)` time complexity, where `L` is the number of words in the query string.
# Properties
- Each trie has an empty root node, with links to other nodes.
- Each node of a trie represents a string and each edge represent a character.
- Every node consists of an array of pointers, with each index represent a character and a flag to indicate if any string ends at the current node.
  
  # Operations
  - `Insert`
  - `Search`
  - `Delete`
  # Complexity

| Operation | Time   | Auxiliary space |
| --------- | ------ | --------------- |
| Insertion | `O(n)` | `O(n*m)`        |
| Search    | `O(n)` | `O(1)`          |
| Delete    | `O(n)` | `O(n*m)`        |

  