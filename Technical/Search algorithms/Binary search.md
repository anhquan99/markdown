# Explain
- Binary search is used in sorted array.
- It split the search space into 2 halves and only keep the half that probably has the search target and throw away the other half. In this manner, the binary search reduce the search space to half size at every step until it find it target.![[Pasted image 20220310230721.png]]
# Advantages
- Simple algorithm
- Compare to linear search it is faster with $O(log(n))$
# Disadvantages
- Overkill for every small numbers of elements
- Work only in sorted list
- Not support random access
- $O(N)$
# Coding
![[Pasted image 20220311150105.png]]