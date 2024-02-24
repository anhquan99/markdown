# Definition 
- Is a design pattern, designed to lossy coupling classes to each others.
- If class A uses some methods from class B, we can say class A is depending on class B. Now to remove this dependency, we use Dependency injection.
- Using DI will inject the dependency in the run time instead by compile time.
# How to use
- Classes will not direct depend on each other, instead they connect by interfaces or base class.
- The initialization of class will be done by interface instead of the dependent class.
# Function
- Create objects.
- Know which objects a class need.
- Provide objects for that class.
# Advantages
- Easy to test because we can mock and inject dependency we want.
- Easy to scale application or functions
- Lossy coupling components.
- Easy to see relationship between objects. 
# Disadvantages
- Hard to learn and if over use it can lead to other problem
- Compile time error will be change to run time error
- Because of using interface so when debug code we can't see which implement is injected.
# Type
- Constructor injection: inject through constructor
- Setter injection: inject through in setter method.
- Interface injection: classes implement from an interface. That interface has `setDependency()` method, so classes implement from it must define that method. This is where the dependency is injected.
``````C#
interface IDataAccessDependency
{
    void SetDependency(ICustomerDataAccess customerDataAccess);
}

public class CustomerBusinessLogic : IDataAccessDependency
{
    ICustomerDataAccess _dataAccess;

    public CustomerBusinessLogic()
    {
    }

    public string GetCustomerName(int id)
    {
        return _dataAccess.GetCustomerName(id);
    }
        
    public void SetDependency(ICustomerDataAccess customerDataAccess)
    {
        _dataAccess = customerDataAccess;
    }
}

public class CustomerService
{
    CustomerBusinessLogic _customerBL;

    public CustomerService()
    {
        _customerBL = new CustomerBusinessLogic();
        ((IDataAccessDependency)_customerBL).SetDependency(new CustomerDataAccess());
    }

    public string GetCustomerName(int id) {
        return _customerBL.GetCustomerName(id);
    }
}
``````