- **Covariance and contravariance enable implicit reference conversion for array types, delegate types and generic type arguments.
# Covariance
-  Preserves assignment compatibility
```c#
// Assignment compatibility. 
string str = "test"; // An object of a more derived type is assigned to an object of a less derived type. 
object obj = str;


// Covariance. 
IEnumerable<string> strings = new List<string>(); 
// An object that is instantiated with a more derived type argument 
// is assigned to an object instantiated with a less derived type argument. 
// Assignment compatibility is preserved. 
IEnumerable<object> objects = strings;
```
# Contravariance
- Reverses assignment compatibility
```c#
// Assume that the following method is in the class
// static cvoid SetObject(object o){}
Action<object>> actObject = SetObject;
// An object that is instantiated with a less derived type argument
// is assigned to an object instantiated with a more derived type argument.
Action <string> actString = actObject;
```
## Contravariant generic delegate
- The `in` keyword for generic interface
```c#
// Contravariant delegate
public delegate void DContravar<in A>(A argument);
// Methods that match the delegate signature
public static void SampleControl(Control control);{}
public static void SampleButton(Button button){}

public void Test(){
	// Instantiating the delegates with the methods
	Dcontravariant<Control> dControl = SampleControl;
	DContravariant<Button> dButton = SampleButton;

	// You can assign dControl to dButton
	// because the DContravariant delegate is contravariant
	dButton = dControl;

	// Invoke thge delegate
	dButton(new Button());
}
```
- This pattern can be apply to [[Technical/Languages/CSharp/Fundamental/Specification pattern]]