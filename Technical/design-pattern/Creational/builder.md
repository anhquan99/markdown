# Builder
- A creational design pattern that lets you construct complex objects step by step. The pattern allows you to produce different types and representations of an object using the same construction code.
## Problem
- A complex object that requires laborious, step-by-step initialization of many fields and nested object. Such initialization code is usually buried inside a monstrous constructor with lots of parameters. Or even worse: scattered all over the client code.
## Solution
- The builder pattern suggests that you extract the object construction code out of its own class and move it to separate objects called builder.
### Director
- A director class helps construct a class in order of execute build steps, while the builder provide implementation for those steps.
- **It's not necessary to have a directory.** But the director class might be a good place to put various construction routines so you can reuse them across your program or completely hides the details of product construction from the client code, the client only needs to associate a builder with a director, launch the construction with the director, and get the result from the builder.
## Applicability
- Get rid of a **telescoping constructor**.
```csharp
class Pizza {
    Pizza(int size) { ... }
    Pizza(int size, boolean cheese) { ... }
    Pizza(int size, boolean cheese, boolean pepperoni) { ... }
    // ...
}
```
- Able to create different representations of some product (ex: stone and wooden houses).
- Construct a [[composite]] trees or other complex objects.
## Steps
1. Make sure that you can clearly define the common construction steps for building all available product representations. Otherwise, you won’t be able to proceed with implementing the pattern.
2. Declare these steps in the base builder interface.
3. Create a concrete builder class for each of the product representations and implement their construction steps. Don’t forget about implementing a method for fetching the result of the construction. The reason why this method can’t be declared inside the builder interface is that various builders may construct products that don’t have a common interface. Therefore, you don’t know what would be the return type for such a method. However, if you’re dealing with products from a single hierarchy, the fetching method can be safely added to the base interface.
4. Think about creating a director class. It may encapsulate various ways to construct a product using the same builder object.
5. The client code creates both the builder and the director objects. Before construction starts, the client must pass a builder object to the director. Usually, the client does this only once, via parameters of the director’s class constructor. The director uses the builder object in all further construction. There’s an alternative approach, where the builder is passed to a specific product construction method of the director.
6. The construction result can be obtained directly from the director only if all products follow the same interface. Otherwise, the client should fetch the result from the builder.
## Pros and cons

| Pros                                                                                                                 | Cons                                                                                                   |
| -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| You can reuse the same construction code when building various representations of products.                          | The overall complexity of the code increases since the pattern requires creating multiple new classes. |
| You can construct objects step-by-step, defer construction steps or run steps recursively.                           |                                                                                                        |
| _Single Responsibility Principle_. You can isolate complex construction code from the business logic of the product. |                                                                                                        |

## Code example
```csharp
public class House()
{
    private List<string> _parts = new List<string>();
    public void Add(string part)
    {
        _parts.Add(part);
    }
    public string ListParts()
    {
        var sb = new StringBuilder();
        foreach (var part in _parts)
        {
            sb.AppendLine(part);
        }
        return sb.ToString();
    }
}
public interface IBuilder
{
    void BuildRooms();
    void BuildAccessories();
}
public class HouseBuilder
{
    private House _house;
    private void Reset()
    {
        _house = new House();
    }
    public HouseBuilder()
    {
        this.Reset();
    }


    public House GetHouse()
    {
        var result = _house;
        this.Reset();
        return result;
    }

    public void BuildRooms()
    {
        _house.Add("Kitchen");
        _house.Add("Bedroom");
    }

    public void BuildAccessories()
    {
        _house.Add("Windows");
        _house.Add("Doors");
    }
}
public class HouseDirector
{
    private IBuilder _builder;
    public IBuilder Builder
    {
        set { _builder = value; }
    }
    public void BuildFlat()
    {
        _builder.BuildRooms();
    }
    public void BuildApartment()
    {
        _builder.BuildRooms();
        _builder.BuildAccessories();

    }
}
class Program
{
	static void Main(string[] args)
	{
		var director = new HouseDirector();
		var builder = new HouseBuilder();
		director.Builder = builder;
		
		director.BuildFlat();
		Console.WriteLine(builder.GetHouse().ListParts());
		
		director.BuildApartment();
		Console.WriteLine(builder.GetHouse().ListParts());
		
		builder.BuildAccessories();
		builder.BuildRooms();
		Console.WriteLine(builder.GetHouse().ListParts());
	}
}
```
## References
- https://refactoring.guru/design-patterns/builder