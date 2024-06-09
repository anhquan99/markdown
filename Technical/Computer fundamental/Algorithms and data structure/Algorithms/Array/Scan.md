# Prefix sum
- Also called cumulative sum or inclusive scan.
## Idea
- When you want to find a sum of array from 0 to n where you will have to sum all the element from 0 to n. If you have to query a lot of sum in the array, the solution above is performant. 
- Calculate the sum of the array index, it represents the sum of the first item in the array to the index in the array.
- After doing prefix sum you will get an array of sum where each element represents a sum of 0 to that index.
- We can query the sum of the ranged index base on the `sum of arr i to j = arr[j] - arr[i - 1]` where i < j.
```
Array:             [1 ,2, 3 ,4, 5]
Prefix sum array:  [0, 1 ,3, 6, 10 ,15] (added 0 to the first index)
Each index present the sum of Array[0] to Array[index]
Sum of index i and index j where i < j: Array[j] - Array[i - 1]
Example: sum of index from 2 to 3 = Array[3] - Array[1] = 6 - 1 = 5
```
## Run time
- `O(n)` because we can access sum of element in constant time.
# Suffix sum
- Likes the idea of prefix sum the suffix sum will start from the end and to the beginning of the array.
