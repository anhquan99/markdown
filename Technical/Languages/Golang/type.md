# Type
## Conversion
- Like **type casting** is done via function with the name of the type convert.
```go
var x int = 42
f := float(x)
```
## Assertions
- Interfaces in Go can introduce ambiguity about the underlying type. A type assertion allows us to extract the interface value's underlying concrete value using syntax `interfaceVariable.(concreteType)`.
```go
var input interface{} = 12
number := input.(int)
```

```ad-note
This will cause a panic if the interface variable does not hold a value of the concrete type. To prevent a panic, there is another return which is in boolean that report the assertion succeeded.

`str, ok := input.(string)`
```
## Type switches
- Perform several type assertions in series.
```go
var i interface{} = 12

switch v := i.(type) {
case int:
	fmt.Print("int")
case string:
	fmt.Print("string")
default:
	fmt.Print("Something else")
}
```
## Non-struct types
- Define a non-struct types which you can use as an alias for a built-in type declaration, and you can define receiver functions on them to extend them in the same way as struct types.
```go
// define struct
type File struct {
	Name string
}

// define non-struct
type File string
```

