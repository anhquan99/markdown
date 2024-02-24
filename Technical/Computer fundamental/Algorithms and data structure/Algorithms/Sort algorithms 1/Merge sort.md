# Idea
1. Find middle point to divide the array into two halves: middle $m = l + (r - 1) / 2$
2. Call merge sort for first half `mergeSort(arr, l, m)`
3. Call merge sort for second half `mergeSort(arr, m + 1, r)`
4. Merge the two halves sorted `merge(arr, l, m, r)`
![[Pasted image 20220311141029.png]]
# Advantages
- If the array is nearly in order, algorithm will run very fast
# Disadvantages
- Big time complexity for big input data.
# Coding
![[Pasted image 20220311141720.png]]