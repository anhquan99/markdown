# Factory Method
## Problem
- Your first version application handle only 1 type of concrete class, then it evolves which add more concrete class to handle. Adding more class to the system requires making changes to the entire codebase and later the scenario happens again.
## Solution
- The Factory Method pattern suggests that you replace direct object construction calls with calls to a special *factory* method.
- You can override the factory method in a subclass and change the class of concrete class being created by method.
- **Limitation**: subclasses may return different types of products only if these products have a common base class or interface, and the factory method in the base class should have its return type declared as this interface.
![[image-5.png]]
## Applicability
- Use the Factory Method when you don’t know beforehand the exact types and dependencies of the objects your code should work with.
- Use the Factory Method when you want to provide users of your library or framework with a way to extend its internal components.
- Use the Factory Method when you want to save system resources by reusing existing objects instead of rebuilding them each time.
## Pros and cons

| Pros                                                                                                                                        | Cons                                                                                                                                                                                                                            |
| ------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| You avoid tight coupling between the creator and the concrete products.                                                                     | The code may become more complicated since you need to introduce a lot of new subclasses to implement the pattern. The best case scenario is when you’re introducing the pattern into an existing hierarchy of creator classes. |
| _Single Responsibility Principle_. You can move the product creation code into one place in the program, making the code easier to support. |                                                                                                                                                                                                                                 |
| _Open/Closed Principle_. You can introduce new types of products into the program without breaking existing client code.                    |                                                                                                                                                                                                                                 |
## Code
```csharp
public interface IProduct  
{  
    string Operation();  
}
  
public abstract class Creator  
{  
    public abstract IProduct FactoryMethod();  
  
    public string SomeOperation()  
    {        var product = FactoryMethod();  
        var result = "Creator: The same creator's code has just worked with: " + product.Operation();  
        return result;  
    }
}  
  
public class ProductCreator1 : Creator  
{  
    public override IProduct FactoryMethod()  
    {        return new ConcreteProduct1();  
    }
}  
  
public class ProductCreator2 : Creator  
{  
    public override IProduct FactoryMethod()  
    {        return new ConcreteProduct2();  
    }
}
public class ConcreteProduct1 : IProduct  
{  
    public string Operation()  
    {
	    return "{Result of ConcreteProduct1}";  
    }
}  
public class ConcreteProduct2 : IProduct  
{  
    public string Operation()  
    {
	    return "{Result of ConcreteProduct2}";  
    }
}
class Program
{
	static void Main(string[] args)
	{
		Console.WriteLine("Creator 1:");  
		ClientCode(new ProductCreator1());  
		  
		Console.WriteLine("Creator 2:");  
		ClientCode((new ProductCreator2()));
	}
	public void ClientCode(Creator creator)  
	{  
	    Console.WriteLine("Client code: " + creator.SomeOperation());  
	}
}

```