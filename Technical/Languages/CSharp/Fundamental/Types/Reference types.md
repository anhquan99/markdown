# `record`
- `record` is a reference type (like a `class`) or a value type (like a `struct`) that's specifically designed to make working with data models easier. It provides a compact syntax and built-in behaviors that are often useful for representing data structures.
- Default `record` is `record class` but the `class` keyword is optional.
## Positional syntax for property definition
- You can use positional parameters to declare properties of a record and to initialize the property values when you create an instance.
- Note when you use the positional syntax for property definition, the compiler creates :
    - A `Deconstruct` method with an out parameter for each positional parameter provided in the record declaration. The method deconstructs properties defined by using positional syntax; it ignores properties that are defined by using standard property syntax.
```c#
Person person = new("Nancy", "Davolio");
public record Person(string FirstName, string LastName);
```
- Add attributes to positional properties:
```c#
public record Person([property: JsonPropertyName("firstName")] string FirstName, [property: JsonPropertyName("lastName")] string LastName);
```
## Value equality:
- If you don't override or replace equality methods, the type you declare governs how equality is defined:
    - For class types, two objects are equal if they refer to the same object in memory.
    - For struct types, two objects are equal if they are of the same type and store the same values.
    - For types with the record modifier (record class, record struct, and readonly record struct), two objects are equal if they are of the same type and store the same values.
- The difference equality of `record struct` and `struct`:
    - `struct` uses `ValueType.Equals(Object)`.
    - `record struct` uses compiler synthesized and declared data members.
## Immutability
- Positional properties are *immutable* in `record class` and `readonly record struct`. They're *mutable* in a `record struct`
- Immutability can be useful when you need a data-centric type to be thread-safe or you're depending on a hash code remaining the same in a hash table.
## Nondestructive mutation
- Copy an instance with some modification, use `with` expression.
- The result of a `with` expression is a *shallow copy*, which copy the value properties but still reference to the same instance.
```c#
Person person2 = person with { FirstName = "Janet" };
```
## Applications
- API response models.
- Configuration settings.
- Domain models.
- When to Choose a Record Even with Reference Type Properties:
    - Data Transfer Objects (DTOs)
    - Value Objects
    - Immutable Models
## Inheritance
- Only apply for `record class`.
# `class`
- **C# does not support multiple inheritance, but a class can implement multiple interfaces.**
## Constructors
## Private constructor
- A private constructor is a special instance constructor. It is generally used in classes that contain static members only.
- Used to prevent creating instances of a class when there are no instance field or methods.
## Static constructor
- Used to initialize any static data.
- Called only once.
## Finalizer
- Used to perform any necessary final clean-up when a class instance is being collected by the garbage collector.
- Empty finalizers should not be used. When a class contains a finalizer, an entry is created in the Finalize queue. This queue is processed by the garbage collector. When the GC processes the queue, it calls each finalizer. Unnecessary finalizers, including empty finalizers, finalizers that only call the base class finalizer, or finalizers that only call conditionally emitted methods, cause a needless loss of performance.
- It's possible to force garbage collection by calling Collect, but most of the time, this call should be avoided because it may create performance issues.
## Method
- Parameters vs arguments:

| Feature        | Parameter               | Argument                        |
| -------------- | ----------------------- | ------------------------------- |
| Definition     | Placeholder in function | Actual value passed to function |
| Location       | Function declaration    | Function call                   |
| Representation | Variable name           | Data (literal, variable, etc.)  |

## Indexers
- Indexers allow instances of a class or struct to be indexed just like arrays
```c#
using System;

var stringCollection = new SampleCollection<string>();
stringCollection[0] = "Hello, World";
Console.WriteLine(stringCollection[0]);

class SampleCollection<T>
{
   // Declare an array to store the data elements.
   private T[] arr = new T[100];

   // Define the indexer to allow client code to use [] notation.
   public T this[int i]
   {
      get { return arr[i]; }
      set { arr[i] = value; }
   }
}
```
## Indexer in interfaces
- Interface accessors do not use modifiers.
- An interface accessor typically does not have a body.
```c#
// Indexer on an interface:
public interface IIndexInterface
{
    // Indexer declaration:
    int this[int index]
    {
        get;
        set;
    }
}

// Implementing the interface.
class IndexerClass : IIndexInterface
{
    private int[] arr = new int[100];
    public int this[int index]   // indexer declaration
    {
        // The arr object will throw IndexOutOfRange exception.
        get => arr[index];
        set => arr[index] = value;
    }
}
```
## Nullable reference types
- For *nullable aware context*.
- Nullable reference types, the null static analysis warnings, and the null-forgiving operator are optional language features.
## Collections
- Collection characteristics:
	- Element access.
	- Performance profile.
	- Grow and shrink dynamically.
## Indexable collection
- Access each element using its index.
## Key/value pair collections
- Access each element using key.
## Iterators
- Used to perform a custom iteration over a collection.
- An iterator uses a `yield return` statement to return each element of the collection one at a time.