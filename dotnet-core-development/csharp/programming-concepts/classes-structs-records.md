# ⭐ Classes, Structs, and Records

[[dotnet-core-development/csharp/programming-concepts/classes-structs-records/polymorphism|Polymorphism]]
[[dotnet-core-development/csharp/programming-concepts/classes-structs-records/members|Members]]
[[dotnet-core-development/csharp/programming-concepts/classes-structs-records/properties|Properties]]
[[dotnet-core-development/csharp/programming-concepts/classes-structs-records/methods|Methods]]
[[dotnet-core-development/csharp/programming-concepts/classes-structs-records/constructors|Constructors]]
[[classes-structs-records#Structs|Structs]]
[[classes-structs-records#Finalizers|Finalizers]]
[[classes-structs-records#Object and Collection Initializers|Object and Collection Initializers]]
	[[classes-structs-records#Object Initializer|Object Initializer]]
	[[classes-structs-records#Collection Initializer|Collection Initializer]]
[[classes-structs-records#Nested Type|Nested Type]]
[[classes-structs-records#Partial classes and methods|Partial classes and methods]]

## Structs

---

- Derived from `System.ValueType`
- Useful for:
    - Representing values whose data requirements are small
    - Passing values as value parameters to methods that have strongly typed parameters.
- They cannot directly inherit from another type
- Are `sealed` by nature, meaning they cannot get inherited.
- Does not require constructors.

## Finalizers

---

- Formerly known as “destructors”
- Cannot be defined in structs.
- Only one finalizer can defined per class.
- Are invoked automatically by the garbage collector.
- Does not have modifiers and parameters.
- Below is the syntax for defining a finalizer

```jsx
class Car
{
	~Car()
	{
		// Cleanup statements.
	}
}
```

- Behind the scenes, the finalizer generates the method `Finalize()` where the finalizer code is placed.
    
- Finalizers being called depend on the implementation of .NET the application is targeting.
    - For .NET Framework, finalizers are called from the most derived to the least derived (the base class)
    - For .NET Core, .NET 5 and later versions, finalizers are not called once the application terminates.

## Object and Collection Initializers

---

### Object Initializer

- Provides a nice and easy way of declaring property values to a class or type without the use of constructors.    
- Extremely useful for declaring anonymous types in LINQ projection or data mapping statements.
- Applicable for indexer implementations.
- Use the `required` modifier for when a property must be defined using the object initializer.
- Use the `init` modifier for when a property must not change its value after initialization, either by constructor or by object initializer.

### Collection Initializer

- You can add null value to collection initializer only if the type allows it, meaning the type is a nullable type.
- Explicit key and value pairings, or indexed elements, can be specified if the collection allows it (e.g. Dictionary)
    - You can choose between the two syntaxes.
        
        ```csharp
        var numbers = Dictionary<int, string>
        {
        	[7] = "seven",
        	[9] = "nine"
        };
        ```
        
        ```csharp
        var numbers = Dictionary<int, string>
        {
        	{ 7, "seven" },
        	{ 9, "nine" }
        };
        ```
        
- When a list property is set as read-only, or without a set accessor, C# still allows the values to be added to the read-only list by simply omitting the new `<type>()` syntax.
    
- To prevent adding the new `<type>()` every time you define a list of such type, the type must:
    - Inherit from `IEnumerable<T>` interface
    - Implement the required `GetEnumerator()` methods.
    - Implement its own `Add()` method with a parameter list consisting of all the needed properties.

## Nested Type

---

- Class or type defined within another class, struct or interface.
    - _No experience yet with a type defined within an interface_.
- The nested or the inner type has access to members of the containing or outer type.
- To instantiate a nested type, follow the syntax below:

```csharp
public class ContainingType
{
	public class Nested
	{
		...
	}
}

ContainingType.Nested nested = new ContainingType.Nested();
```

## Partial classes and methods

---

### Partial classes

- Used to split a class to multiple files.
- Combined when the source code is compiled.
- Useful for:
    - A team working on a single class. Having the class in multiple files allows multiple developers to work on that class simultaneously.
    - Using source generators to generate additional functionality in class.
- To use, define a class with the `partial` keyword.
    - All parts should have the same access level.
    - All parts should share the same feature: If one part of the partial class is sealed, the entire class is sealed. If one part of the partial class is abstract, the entire class is abstract.
- At compile time, the following elements assigned on these partial classes will be combined, and the resulting type will contain all the assigned attributes on their partial state:
    - [[reflection-attributes|Attributes]]
    - [[interfaces|Interfaces]]
    - Members

### Partial methods

- A method is split into the following:
    - In one file, a partial method will only contain the method signature.
    - In another file, a partial method will have its signature as well as its implementation.
    - It is important that the partial classes have the same method signature in order for the compiler to determine which partial classes to merge.
- Can only be a void method.
- Does not have access modifier.
- Does not have `out` parameters.
- Does not contain the following modifiers: `virtual` `override` `sealed` `new` `extern` ^4b0e49
- Can contain the following modifiers: `static` `unsafe`
- Can be generic