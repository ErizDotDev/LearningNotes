# Polymorphism

---

## Versioning with the `override` and `new` keywords

- Derived classes can contain methods with the same name as base class methods:
    - If the method in a derived class is not preceded by the `new` or `override` keywords, the method will still behave as if a `new` keyword was added to the method BUT the compiler will issue a warning.
    - If the method in the derived class is preceded by the `new` keyword, the method is defined as independent of the method in the base class.
        - Additionally, using the `new` keyword hides the base class implementation from the derived class.
            - Does this mean that calling the base class method implementation via `base.MethodCall()` will not work?
    - If the method in the derived class is preceded by the `override` keyword, objects in that class will call that method instead of the method on the base class.
    - A base class method must have the `virtual` keyword to denote that the derived class should override said method.
    - The base class method can be called from within the derived class using the `base` keyword.
    - The `override`, `virtual` and `new` keywords can also be applied to properties, indexers and events.

- By default, C# methods are not virtual.
    

### Override and method selection

- Given the below example:

```csharp
public class Derived : Base
{
	public override void DoWork(int param) {}
	public void DoWork(double param) {}
}
```

- When `DoWork()` is called on an instance of `Derived`, C# compiler will always try to make the call compatible with the versions of `DoWork` declared originally in `Derived`, ergo the `public void DoWork()` method.
- If the C# compiler cannot match the method call from the non override methods of the same name in that class, then that’s the time it will consider the override methods in that class.
- For example, if `Derived.DoWork()` method was called with an int value parameter, compiler will still call `Derived.DoWork(double)` implementation as the int value can be casted to double.
    - There are a couple of ways to avoid this mistake:
        - Avoid naming non-override methods similar to virtual methods
        - Instruct the C# compiler to call the virtual method by making it search the base class method list.
            - This can happen by casting the Derived class to the Base class which follows the concept of [[contravariance-covariance#^5878ac|contravariance]].