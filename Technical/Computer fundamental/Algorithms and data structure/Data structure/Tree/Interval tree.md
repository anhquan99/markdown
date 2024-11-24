# Idead
- Store the interval data type in the self-balancing Binary Search Tree (BST).
# Structure
- (Low, High) is the interval of a node.
- Max is the maximum high value of the subtree in the node. 
![[Pasted image 20240613093228.png]]
# Operations
- Insert: like self-balance BST, interval tree must update the Max when the interval high value is bigger than the nodes which the interval travel through.
- Search
- Delete
- Interval intersection query:
```
# pseudo code for interval intersection
If interval in node intersects query interval return it
Else if left subtree is null, go right
Else if max endpoint in left subtree is less than low, go right
Else go left
```
# Code
```csharp
public class IntervalNode
{
    public Interval Range;
    public IntervalNode Left, Right;
    public int Max;
    public IntervalNode(Interval range, int max)
    {
        Range = range;
        Max = max;
    }
    public override string ToString()
    {
        return $"{Range.ToString()}, Max: {Max}";
    }
}
public class Interval
{
    public int Low, High;
    public Interval(int low, int high)
    {
        Low = low;
        High = high;
    }
    public bool Intersect(Interval interval)
    {
        return interval.High >= Low && interval.Low >= Low;
    }
    public override string ToString()
    {
        return $"[{Low} - {High}]";
    }
}
public static class IntervalHelper
{
    public static void InOrderTravel(this IntervalNode root)
    {
        if (root is null) return;
        InOrderTravel(root.Left);
        Console.WriteLine(root);
        InOrderTravel(root.Right);
    }
    public static IntervalNode Insert(this IntervalNode root, Interval interval)
    {
        if (root == null) return new IntervalNode(interval, interval.High);
        if (interval.Low < root.Range.Low)
        {
            root.Left = Insert(root.Left, interval);
        }
        else
        {
            root.Right = Insert(root.Right, interval);
        }
        if (root.Max < interval.High)
        {
            root.Max = interval.High;
        }
        return root;
    }
    public static Interval QueryIntersect(this IntervalNode root, Interval interval)
    {
        if (root == null) return null;
        if (root.Range.Intersect(interval)) return root.Range;
        else if (root.Left == null || root.Left.Max < interval.Low) return QueryIntersect(root.Right, interval);
        else return QueryIntersect(root.Left, interval);
    }
}
```