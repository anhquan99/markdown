# Definition
- Hash table always has key and value pairs
- Key always unique but value is not
- Key must be hashable
- Key will be mapped to an index by using [[Hash function]]
# Time complexity
- n/k called <span style='color:aqua; font-weight:bold'>load factor</span>. When load factor small to 1, and hash function distributed evenly. If the buckets are big enough, each bucket only have some elements. This will make searching very efficient O(1). 
# Problem
- When hashing, the index values can be the same. This will make collision for the elements, and it affects the efficiency of the hash table
- There are 2 solutions for this issue:
	- **Separate chaining**: store each element of a bucket as a linked list. If there are some elements have the hash value index equal together, that bucket will store each element as a linked list point to the element come after.
	- **Open Addressing**: 
		- when collision happened, hash table will store that element in the next bucket.
		- <span style="color:red; font-weight: bold;">Attention:</span> when use this solution, make sure the <span style="color:aqua; font-weight: bold">load factor</span> is below 1 or the **Time complexity** will be O(n)
		- Linear probing: check if the next bucket has element yet. If the bucket has element, check the next bucket until find an empty bucket.
		- Quadratic probing: works similar to linear probing, but the spacing between the slot is increased (greater than one).
	- **Double hashing:** if a collision occurs after applying a hash function, then another hash function is calculated for finding the next slot.