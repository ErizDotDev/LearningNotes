# ⭐ Program Building Blocks

### Static vs. Instance [[methods|methods]]

- Static methods can only access static members
- Instance methods can access both static and non-static members
- Static methods will not be able to access `this`

### Why use [[members#Static classes and members|static]]?

- Static classes provides an easy way to access class members with an expectation that behavior will not change regardless of the object that will use it. (example: Math class)
- Good for utility classes
- Use static properties when values for this properties will never change.
- It does not require instances to be created for each new object.

### Compile-time type vs. run-time type

- Run-time type is the actual type that a variable is declared into.
- Compile-time type is prevalent for cases where a base class is instantiated using its derived class.

As an example to compare a compile time type and a run time type, consider the code below.

```csharp
internal class A
{
	internal virtual void Test() => Console.WriteLine("Call Class A Test() method");
}

internal class B : A
{
	internal override void Test() => Console.WriteLine("Call Class B Test() method");
}
```

When we initialize an object given the above classes:

```csharp
A test = new B();
```

The compile time type of the above example is class A. The compiler does not know that we are giving class A an instance of class B.

The run time type of the above example is class B since at run time, the variable test can now recognize that its assigned type is now class B.

### Abstract [[methods|methods]]

- Seen mostly in abstract classes
- Has no implementation details except for its method signature, and should be overridden on the class that will be inheriting the base class.
- After implementing an abstract method, the derived class is expected to implement that method

### [[indexer|Indexers]]

- Useful for when an object is created and an index is needed to access values from this object like an array.

```csharp
public T this[int index]
{
    get => _items[index];
    set
    {
        if (!object.Equals(_items[index], value)) {
            _items[index] = value;
            OnChanged();
        }
    }
}
```

- Can be overloaded, as long as the number or type of the parameters are different.

### [[events|Events]]

- provides a different way to be notified for when a particular action.
- possible use cases: code analytics + logging

### Private setters

- [[properties|Properties]] that act as readonly properties.
- Properties that have private setters can only be initialized in a constructor or on set methods

### Accessibility

- _public_ - most permissive access level; no access restrictions
- _protected_ - accessible within the class and by derived class instances; not accessible by direct instances
- _internal_ - accessible only within files in the same assembly.
- _protected internal_ - accessible from the current assembly or from types that are derived from the containing class
- _private_ - inaccessible by other classes; access is limited to the class/struct that contains it.
- _private protected_ - accessible by derived class instances only within the containing assembly

- NOTE: All accessibility keywords can be applied on class/struct members. However, _protected internal_ and _private protected_ are the only accessibility keywords that cannot be applied on a class definition.