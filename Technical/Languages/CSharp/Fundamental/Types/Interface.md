- Coding convention for naming an interface must begin with a capital `I`.
# Instance members
- Method
- Properties: provide controlled access to data (like variables)
- Events
- Indexers
```c#
public interface IShapeOperations
{
	double CalculateArea(); // instance method
	double Area { get; } // property
	event EventHandler ShapeModified; // event
}
```
# Static member (C# 8+)
- Static constructors: for 1 time initialization of static member
- Static fields: hold shared data for all implementers of the interface
	- Uniqueness: if an interface has a static member and an implementing class also declares a static member with the same signature these are considered separate entities.
```c#
interface IExample
{
    static int Data = 1;
    static IExample()
    {
        Data = 2;
    }
}
class Example : IExample
{
    public int GetData => this.Data; // error because the Data field belongs to the interface
}
```
- Constants
- Operators: define custom operators for types implementing the interface (C# 11+)
```c#
public interface ICounter<T> where T : ICounter<T>
{
    int Value { get; }

    static abstract T operator ++(T counter);
}
public class Counter : ICounter<Counter>
{
    private int _value;
    public int Value
    {
        get => _value * 2;
        set => _value = value;
    }
    public Counter(int value)
    {
        Value = value;
    }

    public static Counter operator ++(Counter counter)
    {
        return new Counter(counter.Value + 1);
    }
}
```
- Static abstract members (C# 11+): like abstract methods but are static, so the implementing type must provide a static implementation
# Restrictions
- Interfaces can not have:
	- Instance fields
	- Instance constructors
	- Finalizer
# Accessibility
- Default public, but you can use explicit modifiers like `private`, `protected`, ...
- Private member with default: private interface members must have a default implementation right in the interface, as the implementer can not provide it.
# Implementation rules
- Public and Non-static