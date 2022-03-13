# What is C# ?
- C# is an object-oriented, type-safe, and managed language that is compiled by .net framework to generate Microsoft Intermediate Language.
# How C# compile 
1. When you run your code, C# compiler takes your code as an input and process it then output your program in **intermediate language (IL)** code which is saved in .exe or .dll files.
2. After the IL code is generated, the **Common Language Runtime (CLR)** convert IL to machine codes.
3. CLR use **just-in-time (JIT)** compiler to translate the IL code into machine code.![[Pasted image 20220313161419.png]]
- ![[Pasted image 20220313160144.png]]
# Const vs static vs readonly
- **Const:** a const variable must be assigned a value at declaration and can't be changed or assigned at run time. Is a compiled time variable
- **Static:** a static keyword make a member of class, class or function, .... static. This means you can't construct a new object for static. With function or variable, static can only access through class, not instance. With class, static can't be initialized with `new` keyword, but static constructor can - it initialize static any static field, **but it runs only 1 time when it first initialized**. Static variable can be changed in run time. But with the **final** keyword, it can't be changed.
- **Readonly:** readonly variable can be assigned at the time of declaration or in constructor, it can't be changed inside a method. Can't declare using static keyword because it is default static. It can't be assigned through the reference variable. Run time constant variable.
# Ref vs out
- **Ref:** an argument passed ref must be initialized before passed into method.
- **Out:** not need to initialized when passed into method.
# Using block
- Create a resource and process it and then automatically dispose when the block completed.
# This command within a static method
- We can't use `this` command in static method because static method can only contain static variable/method in static method 
# Serialization
- When we want to transport an object through a network, then we have to convert the object into a stream of bytes. This process is called Serialization. For an object to be serializable it should implement ISerialize interface. The reverse process is called De-serialization.
# Value type vs reference type
- **Value type:** holds data value within its own memory space.
- **Reference type:** store the address of the object where the value is being stored. It is a pointer to another memory location.
- Object is a reference type.
# Delegate
- Create delegate variable reference to a function with the same input arguments.![[Pasted image 20220313165658.png]]
- Multicast delegate
	``````c#
using System;

// Define a custom delegate that has a string parameter and returns void.
delegate void CustomDel(string s);

class TestClass
{
    // Define two methods that have the same signature as CustomDel.
    static void Hello(string s)
    {
        Console.WriteLine($"  Hello, {s}!");
    }

    static void Goodbye(string s)
    {
        Console.WriteLine($"  Goodbye, {s}!");
    }

    static void Main()
    {
        // Declare instances of the custom delegate.
        CustomDel hiDel, byeDel, multiDel, multiMinusHiDel;

        // In this example, you can omit the custom delegate if you
        // want to and use Action<string> instead.
        //Action<string> hiDel, byeDel, multiDel, multiMinusHiDel;

        // Create the delegate object hiDel that references the
        // method Hello.
        hiDel = Hello;

        // Create the delegate object byeDel that references the
        // method Goodbye.
        byeDel = Goodbye;

        // The two delegates, hiDel and byeDel, are combined to
        // form multiDel.
        multiDel = hiDel + byeDel;

        // Remove hiDel from the multicast delegate, leaving byeDel,
        // which calls only the method Goodbye.
        multiMinusHiDel = multiDel - hiDel;

        Console.WriteLine("Invoking delegate hiDel:");
        hiDel("A");
        Console.WriteLine("Invoking delegate byeDel:");
        byeDel("B");
        Console.WriteLine("Invoking delegate multiDel:");
        multiDel("C");
        Console.WriteLine("Invoking delegate multiMinusHiDel:");
        multiMinusHiDel("D");
    }
}
/* Output:
Invoking delegate hiDel:
  Hello, A!
Invoking delegate byeDel:
  Goodbye, B!
Invoking delegate multiDel:
  Hello, C!
  Goodbye, C!
Invoking delegate multiMinusHiDel:
  Goodbye, D!
*/
``````

# Indexers
- Indexers allow instances of a class or struct to be indexed just like arrays. The indexed value can be set or retrieved without explicitly specifying a type or instance member.
``````c#
using System;

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

class Program
{
   static void Main()
   {
      var stringCollection = new SampleCollection<string>();
      stringCollection[0] = "Hello, World";
      Console.WriteLine(stringCollection[0]);
   }
}
// The example displays the following output:
//       Hello, World.
``````

# Is vs as
- **Is:** operator used to check if an object is the given type. The result return as boolean
- **As:** used for casting an object into specific class or type
# == vs Equal() compare in string
- == compare reference identity
- Equal compare content. 
# Attribute
- Define declarative tags on certain entities class, method,... Retrieved at runtime using Reflection.
- Example:
``````c#
[Route("path")]
public class HomeController{}
``````
# Throw vs throw ex
- Throw preserves original stack
- Throw ex have the stack trace from their throw point
# How we can create an array with non-default values?
- Using `Enumerable.Repeat()`
# Inherited interfaces have conflicting method names?
- Using interface as initialize declaration. In implement method, remove public keyword.
# Finalize() vs Dispose()
- **Dispose()** is called when we want to release unmanaged resources, free memory. Invoke by user.
- **Finalize()** allows an object to try to free resources and perform other cleanup operations before it is reclaimed by garbage collection. Invoke by garbage collector, just before the object is destroyed.
# Array clone() vs copy()
- **Clone()** destination is not required
- **Copy()** destination is required, the array copy need to exist before copy and must be large enough to hold all elements.
# Internal 
- Accessible within the same assembly
# Object pool
- An object pool is a container having objects ready to be used. It tracks the object that is currently in use, total number of objects in the pool. This reduces the overhead of creating and re-creating objects.