# Idea
- Backtrack is an algorithm to get all the solutions in a problem. Each of them is built from a possible child solution.
- It incrementally builds candidates to the solution, and abandons a candidate ("backtracks") as soon as determines that the candidate cannot possibly be completed to a valid solution.
# Example:
- Problem: generate binary number with k length
```c#
void backtrack(int position, int k, List<char> result)
{
	if(position == k)
	{
		// do somthing with result
		return;
	}
	for(int i = 0; i <= 1; i++)
	{
		result.Add(i);
		backtrack(position + 1, k);
		result.RemoveAt(result.Length - 1);
	}
}
```
# Branch bound
- Using backtracking, but you must select the best solution.