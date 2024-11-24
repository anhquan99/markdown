- Non-comparison-based sorting algorithm.
- Efficient when the range of inputs is small compared to the number of elements to be sorted.
# Idea
- Count the frequency of each distinct element in the input array, then cumulate the array and use that element to replace the correct sorted position.
- The original frequency count tells us how many times a specific value appears.
- The cumulative sum tells us how many elements are less than or equal to that value. This is exactly the index where the last occurrence of that value should be placed in the sorted output.
# Advantage
- Counting sort generally performs faster than all comparison-based sorting algorithms, such as merge sort and quick sort, if the range of input is of the order of the number of input.
- Counting sort is easy to code
- Counting sort is a stable algorithm.
# Disadvantage
- Counting sort doesn’t work on decimal values.
- Counting sort is inefficient if the range of values to be sorted is very large.
- Counting sort is not an In-place sorting algorithm, It uses extra space for sorting the array elements.
# Key
- Finding the correct position for each element
- Handling duplicate values correctly
- Maintaining the stability of the sort
- Doing all this while preserving the algorithm's efficiency
```csharp
int[] CountSort(int[] arr)
{
    var max = int.MinValue;
    // find max to setup the count array
    foreach (int i in arr)
    {
        if (i > max)
        {
            max = i;
        }
    }
    int[] count = new int[max + 1];
    // count element in array
    foreach (int i in arr)
    {
        count[i]++;
    }
    // cumulate count
    for (int i = 1; i < count.Length; i++)
    {
        count[i] += count[i - 1];
    }
    int[] output = new int[arr.Length];
    // replace element
    for (int i = arr.Length - 1; i >= 0; i--)
    {
        output[count[arr[i]] - 1] = arr[i];
        count[arr[i]]--;
    }
    return output;
}
```