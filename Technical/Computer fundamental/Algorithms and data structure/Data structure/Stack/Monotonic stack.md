# Idea
- Monotonic stack has the same idea of [[Monotonic Queue]] (same implementation but different data structure).
- The stack need to be satisfied the monotonic condition when pushing the new element into the stack, if it is not pop the top element off the stack until pushing new element into the stack satisfy the monotonic condition.
- Example:
```c#
public static void MonoStack(List<int> insertEntries)
{
    List<int> stack = new List<int>();
    foreach (int entry in insertEntries)
    {
        // The monotonic property can only break if and only if the container
        // is not empty and the last item, compared to the entry, breaks
        // the property. While that is true, we pop the top item.
        while (stack.Count > 0 && stack[stack.Count - 1] <= entry)
        {
            stack.RemoveAt(stack.Count - 1);
            // Do something with the popped item here
        }
        stack.Add(entry);
    }
}
```
# Note
- A monotonic stack stores both the position and value. The indices of the element in the monotonic stack are always increasing. The value of the element are always meet the property of the monotonic stack.