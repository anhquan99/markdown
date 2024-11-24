# Problems
- Solve the running time problem with `O(logn)` time complexity with operations:
	- Update
	- Prefix sum query
# Idea
- Using prefix sum but storing the data in the range representation (power of 2) in an array. Each element represents the sum of a specific range of elements from the original array.
- Each index in the array represent of element whose size is the largest power of 2.
	- Index 1 store sum of the first element.
	- Index 2 store the sum of the first 2 elements.
	- Index 4 store the sum of the first 4 elements.
	- ...
## Bitwise Operations `index & -index`: 
-  It is a clever trick to isolate the lowest set bit in `index`, which determines the range represented by a BIT element.
- How it works:
	- **Negation**: When you negate a number (`-index`), all its bits are flipped, and then 1 is added. The result is that all bits to the right of the least significant '1' bit are flipped, and the least significant '1' bit itself becomes 0.
	- **Bitwise AND:** Performing the bitwise AND between the original `index` and its negated form `-index` will zero out all the bits except the least significant '1' bit. This is because only this bit remains the same (1) in both numbers after negation.
# Operations
- **Query (Prefix Sum):** To find the prefix sum up to a given index, we start at that index in the BIT and repeatedly subtract the least significant bit (LSB) from the index until we reach zero. At each step, we add the value stored in the BIT at the current index. This process effectively accumulates the prefix sum by considering the appropriate ranges.
- **Update:** When you update a value in the original array, you need to update the corresponding ranges in the BIT. You achieve this by starting at the updated index and repeatedly adding the LSB until you exceed the array size. At each step, you update the value stored in the BIT at the current index.
# Consideration
- When the problem can have inverse:
	- The opposite directions of traversal in update and query operations.
	- The potential need for inverse elements in mathematical groups when using BITs with operations other than addition.
	- Inverse operations to handle range updates efficiently.
- For examples of consideration when using binary index tree:
	- Addition has inverse subtraction.
	- Multiplication has inverse division.
	- GCD has no inverse so can not use binary index tree.
	- ...
```csharp
class BIT
{
    private int[] bit;

    public BIT(int size)
    {
        bit = new int[size + 1]; // 1-based indexing
    }

    public void Update(int index, int value)
    {
        index++; // 1-based indexing
        while (index < bit.Length)
        {
            bit[index] += value;
            index += (index & -index); // Move to next relevant index
        }
    }

    public int Query(int index)
    {
        int sum = 0;
        index++; // 1-based indexing
        while (index > 0)
        {
            sum += bit[index];
            index -= (index & -index); // Move to parent index
        }
        return sum;
    }
}
```