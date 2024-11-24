# Attribute and reflection
- Attributes provide a powerful method of associating metadata, or declarative information, with code (assemblies, types, methods, properties, and so forth). After an attribute is associated with a program entity, the attribute can be queried at run time by using a technique called reflection. 
## Attribute
### Parameters
- Parameters on an attribute constructor are limited to simple types/literals like `bool, int, double, ...`
### Properties
- Attributes add metadata to your program. Metadata is information about the types defined in a program. All .NET assemblies contain a specified set of metadata that describes the types and type members defined in the assembly. You can add custom attributes to specify any additional information that is required.
- You can apply one or more attributes to entire assemblies, modules, or smaller program elements such as classes and properties.
- Attributes can accept arguments in the same way as methods and properties.
- Your program can examine its own metadata or the metadata in other programs by using reflection.
### Targets
- The target of an attribute is the entity that the attribute applies to.
```csharp
// default: applies to method
[ValidatedContract]
int Method1() { return 0; }

// applies to method
[method: ValidatedContract]
int Method2() { return 0; }

// applies to parameter
int Method3([ValidatedContract] string contract) { return 0; }

// applies to return value
[return: ValidatedContract]
int Method4() { return 0; }
```
### Generics
- You can apply attributes only on:
    - Open generic types: the type is not specified like `List<T>`
    - Closed constructed generic types: the type is specified like `List<Int>`
```csharp
// open generic types
public class GenericClass1<T> { }

[CustomAttribute(info = typeof(GenericClass1<>))]
class ClassA { }

// closed generic types
public class GenericClass3<T, U, V> { }

[CustomAttribute(info = typeof(GenericClass3<int, double, string>))]
class ClassC { }
```
### Restriction
- Attribute can be restricted for certain targets.
- By default, if you don't restrict any type on an attribute it can be used to any targets.
```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct)]
public class MyAttributeForClassAndStructOnly : Attribute {}
```
### Retrieve attribute
- Initialize the new attribute instance to the value of the attribute you want to retrieve.
#### Single
- Use `Attribute.GetCustomAttribute`.
#### Multiple with same scope
- Use `Attribute.GetCustomAttributes`.
#### Multiple with different scope
- Access each scope to get the custom attributes.
```csharp
var attrs = Attribute.GetCustomAttribute(typeof(TestClass), typeof(TestAttribute));
Console.WriteLine(attrs.ToString());
var t = typeof(TestClass);
var memberInfo = t.GetFields();
foreach (var member in memberInfo)
{
    var attr = Attribute.GetCustomAttribute(member, typeof(TestAttribute));
    Console.WriteLine(attr.ToString());
}
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Field, AllowMultiple = false)]
public class TestAttribute : Attribute
{
    public string Name;
    public TestAttribute(string name)
    {
        Name = name;
    }
    public override string ToString()
    {
        return Name;
    }
}
[Test("Test 1")]
public class TestClass{
    [Test("Test 2")]
    public int Field1;
}
```
## Reflection
- Reflection provides objects (of type Type) that describe assemblies, modules, and types. You can use reflection to dynamically create an instance of a type, bind the type to an existing object, or get the type from an existing object and invoke its methods or access its fields and properties. If you're using attributes in your code, reflection enables you to access them.
### Types
- `Assembly`: used to load assemblies to explore the metadata and constituent parts of assemblies, to discover the types contained in assemblies, and to create instances of those types.
- `Module`: is a portable executable file, such as `.dll or .exe`
- `ConstructorInfo`
- `MethodInfo`
- `FieldInfo`
- `EventInfo`
- `PropertyInfo`
- `ParameterInfo`
- `CustomAttributeData`
### Dynamically load and use types
- `Type.InvokeMember` function is used to invoke member of a type.
- `CreateInstance` function is used create instance of a type.