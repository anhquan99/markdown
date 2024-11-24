# Idea
- **Monotonic** means a list or function is either always increasing, or always decreasing.
- Types:
	- **Increasing monotonic queue**: keeps elements in increasing order, and any element that is smaller than the current minimum is removed.
	- **Decreasing monotonic queue**: keeps elements in decreasing order, and any element that is larger than the current maximum is removed.
- Example of increasing monotonic queue:
```csharp
static LinkedList<int> decreasing_monotonic_queue(int[] arr, int n)
{
	LinkedList<int> q = new LinkedList<int>();
	for (int i = 0; i < n; i++)
	{
		// If recently added element is
		// smaller than current element
		while (q.Count > 0 && q.Last.Value < arr[i])
		{
			q.RemoveLast();
		}
		q.AddLast(arr[i]);
	}
	return q;
}
```
# Application
- Finding max or min of element in sliding window.
- Solving dynamic programming problems, LIS (longest increasing subsequence) and LDS (longest decreasing subsequence).