# Integral numeric
- `nint` and `nuint` range depends on platform (compute at runtime).
## Literals
- *decimal*: without any prefix.
- *hexadecimal*: with the `0x` or `0X` prefix.
- *binary*: with the `0b` or `0B` prefix.
```csharp
var decimalLiteral = 42;
var hexLiteral = 0x2A;
var binaryLiteral = 0b_0010_1010;
```
## Floating-point numeric

| Type      | Percision     | Size     | Usage                                                                      |
| --------- | ------------- | -------- | -------------------------------------------------------------------------- |
| `float`   | ~6-9 digits   | 4 bytes  |                                                                            |
| `double`  | ~15-17 digits | 8 bytes  |                                                                            |
| `decimal` | 28=29 digits  | 16 bytes | For degree of precision, such as financial applications, currency amounts. |
- The `float` and `double` has:
	- `NaN` is return when the result of a method or operation is undefined.
	- `PositiveInfinity` is return when the result of a method or operation is greater than max value.
	- `NegativeInfinity`is return when the result of a method. or operation is smaller than min value
```csharp
Double zero = 0;
  
// This condition will return false.
if ((0 / zero) == Double.NaN)
   Console.WriteLine("0 / 0 can be tested with Double.NaN.");
else
   Console.WriteLine("0 / 0 cannot be tested with Double.NaN; use Double.IsNan() instead.");
   
// This will equal Infinity.
Console.WriteLine("PositiveInfinity plus 10.0 equals {0}.", (Double.PositiveInfinity + 10.0).ToString());

// This will equal Infinity.
Console.WriteLine("10.0 minus NegativeInfinity equals {0}.", (10.0 - Double.NegativeInfinity).ToString());
```
## Literals
- The literal without suffix or with the `d` or `D` suffix is of type `double`
- The literal with the `f` or `F` suffix is of type `float`
- The literal with the `m` or `M` suffix is of type `decimal`
# Tuple
- The _tuples_ feature provides concise syntax to group multiple data elements in a lightweight data structure.
```csharp
(double, int) t1 = (4.5, 3);
Console.WriteLine($"Tuple with elements {t1.Item1} and {t1.Item2}.");
// Output:
// Tuple with elements 4.5 and 3.

(double Sum, int Count) t2 = (4.5, 3);
Console.WriteLine($"Sum of {t2.Count} elements is {t2.Sum}.");
// Output:
// Sum of 3 elements is 4.5.
```
## Types
### `Tuple`
- Is a reference type.
- Immutable.
- Create using `Tuple.Create` method or a constructor.
- Performance overhead due to heap allocation.
### `ValueTuple`
- Is a value type.
- Mutable.
- Create using parentheses.
- More efficient than `Tuple` due to stack allocation.
## Application
- Replace the `out` method parameters.
- Use deconstruct.
- Replace the anonymous types.
	- Tradeoffs:

| Name            | Access modifier | Type     | Custom member name | Deconstruction support | Expression tree support |
| --------------- | --------------- | -------- | ------------------ | ---------------------- | ----------------------- |
| Anonymous types | `interal`       | `class`  | Yes                | No                     | Yes                     |
| Tuple           | `public`        | `class`  | No                 | No                     | Yes                     |
| ValueTuple      | `public`        | `struct` | Yes                | Yes                    | No                      |

## Equality
- Tuple types support the `==` and `!=` operators. These operators compare members of the left-hand operand with the corresponding members of the right-hand operand following the order of tuple elements.
```csharp
(int a, byte b) left = (5, 10);
(long a, int b) right = (5, 10);
Console.WriteLine(left == right);  // output: True
Console.WriteLine(left != right);  // output: False

var t1 = (A: 5, B: 10);
var t2 = (B: 5, A: 10);
Console.WriteLine(t1 == t2);  // output: True
Console.WriteLine(t1 != t2);  // output: False
```