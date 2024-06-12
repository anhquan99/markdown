# Idea
- Using the idea of the [[Trie]] come with improvement in the space complexity.
- The ternary search tree only contains 3 pointers:
	- The left pointer is the node whose value is less than the value in the current node.
	- The equal pointer is the node whose value is equal to the value in the current node.
	- The right pointer is the node whose value is greater than the value in the current node.
# Structure
- Data
- End flag indicates the end of a sequence
- Left pointer
- Equal pointer
- Right pointer
# Applications
- Near-neighbor lookups, auto complete.
- Spell check.