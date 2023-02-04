# Idea
1. Choose a pivot
2. Divine into 2 halves
	- All element is bigger than or equal pivot 
	- All element is smaller than pivot
3. Then repeat until there is no more element in sub halves
- ![[Pasted image 20220311143308.png]]
- We can choose pivot in these position:
	- At first element
	- Last element
	- Random element 
	- Median element
- # Advantages
	- The fastest sort algorithm 
# Disadvantages
- Depending on the data which make the division is the worst case or best case.
- Worst case: $O(N^2)$
- Average and best case: $O(NlogN)$
# Coding
![[Pasted image 20220311144241.png]]