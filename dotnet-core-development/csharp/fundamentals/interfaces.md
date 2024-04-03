# ⭐ Interfaces

- Interfaces is used to define a behavior of a class which does not necessarily have a “is a” relationship.
- For C# versions earlier than 8.0, interfaces are similar with abstract classes without the properties definition. No default implementation for any defined methods was necessary.
- For C# versions starting at 8.0, default interfaces is available, meaning a default implementation can be defined on methods that are registered in an interface. Properties can also be defined inside that same interface.

### Why do we need to use default interfaces?

[https://stackoverflow.com/questions/62832992/when-should-we-use-default-interface-method-in-c](https://stackoverflow.com/questions/62832992/when-should-we-use-default-interface-method-in-c)

For backwards compatibility. Given a scenario that you are maintaining a library used by a lot of people and this library contains an interfaced named A, a method is needed to be added to interface A.

You cannot simply add another method in the interface as it will break classes that implements the interface. You need to revisit these classes and add an implementation to the newly added method. (~~Personally, I think the easiest approach would be to revisit the classes and add the method. Inside the method, we simply throw a `NotImplementedException` if these classes are not expected to adopt this method widely.~~ The more I think about it, this is not looking as a best practice)

Converting the interface into an abstract base class is also a no-go. This will break a lot of code, as stating it simply.

---

### Default Interfaces

- Interfaces can now include static members both fields and methods.
- Access modifiers are also enabled.
- A method default implementation can be hidden by using the `protected static` modifier.
- Default interface methods does not need the `override` keyword although it functions the same way as a virtual method.

- Avoid overriding the same method in different derived interfaces.
    - In the event that a class inherits from all the derived interfaces and that both these derived interfaces have overridden the same method and the class calls that exact same overridden method, then the compiler will issue an error because it cannot choose which implementation to run.
        - In this case, code should be explicit as to what implementation should be followed from the derived interfaces.
        - Additionally, it will be best if the interfaces are small and focused.
        - Class is the only one that can inherit multiple interfaces.

- The following branches in the `DC.Lab` solution exhibit code that demonstrates default interfaces.
    - `default-interfaces`
    - `default-interfaces-2`

**Extension methods**

- An alternative of using default interface is the extension method pattern.
- The extension method pattern has limitations:
    - The variable’s declared type must match the type that the extension method is defined on
        - If the declared type was an interface and the extension method was defined in that interface’s type, there is no issue.
        - However, given that the extension method is still defined in the interface’s type, if the declared type is a class that implements that interface (which is possible in code) and it tries to call the extension method, that will not work.
    - The extension method will always be available when it is called on the concerned type.
        - It cannot be disabled.

---

### Explicit interface

- In case a class inherits from two interfaces, and each of these interfaces have a method of the same method signature, the class needs to explicitly know which interface method it will implement.
    - It will get confusing to track which interface that class method is implementing.
- Explicit interface is also useful for when interfaces has members that share the same name, this will allow the developer to specify which member is going to be implemented and from which interface.
    - In this case, this will avoid compiler errors.
    - Both members can be implemented so long as the interface is specified during its implementation.
- Simply follow this syntax:

```csharp
public class IControl
{
	void Paint();
}

public class ISurface
{
	void Paint();
}

public class Sample : IControl, ISurface
{
	public void IControl.Paint()
	{
		Console.WriteLine("Paint method in SampleClass using IControl interface.");
	}

	public void ISurface.Paint()
	{
		Console.WriteLine("Paint method in SampleClass using ISurface interface.");
	}
}
```

- To instantiate a class and use explicit interface:
    - The class will need to be instantiated using its own type and not by the interface.
    - Initialize the interface type by assigning the instantiated class as its value.