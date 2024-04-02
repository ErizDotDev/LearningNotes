# Generics

---

- Allows you to tailor a class, method, structure or interface to a specific data type.
- Provides strong typing to specific data structures (e.g. using `Dictionary<TKey, TValue>` instead of `Hashtable`.

## Parameter Constraints

- This is the portion of code in a generic class definition with the `where` keyword and the parameter type where a constraint value needs to be provided.

- Following constraint values are available:
    - struct
    - class
    - class? (class can be nullable or non-nullable)
    - `notnull` - non-nullable types
    - `default` - can be class or struct
        - Used only for methods
    - unmanaged - non-nullable unmanaged types like struct
        - Useful for writing methods that manipulates blocks of memory.
    - `new()` - type must have a parameterless constructor
        - Should be specified list if used together with other constraints
    - nullable or non-nullable base class type
    - interface
    - `T : U` - T should be or derived from type U.
        - T must follow nullability constraint of U - meaning if U is non-nullable, T should also be non-nullable. Same goes for nullable.

- When using a class constraint, avoid using operators `==` and `!=` since it will test for reference equality and not value equality.
    - Even if the operators’ behavior are overridden, it will still follow the default behavior of the mentioned operators which is to test equality based on references.
        - This is because the operators are not virtual by default, meaning if the operators are invoked on a reference type, the values will be resolved statically based on compile time type rather than dynamically based on runtime types.

- Can apply constraints to multiple generic type parameters.
- Generic type parameters that do not have any constraints are called **unbounded type parameters**.

- Unbounded type parameter from a generic class can be used as a constraint from a type parameter found in a generic method inside the generic class.
    - Use this technique, type parameters as constraints, in generic class only on scenarios to validate inheritance relationship between two type parameters.

- A branch in `DC.Lab` solution named `csharp/generic/constraint` contains code demonstrating some features discussed in this section.
    

## Generic Classes

- Generic class inheritance types
    - Concrete - Non-generic class
    - Closed constructed - `ClosedGeneric<int>`
    - Open constructed - `OpenGeneric<T>`

- Some rules on generic class inheritance:    
    - Concrete can inherit from closed constructed base classes but not from open constructed base classes, or from base types, for example `T`.
    - Generic classes that inherit from open constructed types must supply the type arguments for any base class type parameters shared by the inheriting class.
        - Successful code examples:
            - `class Node<T> : BaseNodeMultiple<T, int>`
            - `class Node<T, U> : BaseNodeMultiple<T, U>`
        - Code examples that will result to an error:
            - `class Node<T> : BaseNodeMultiple<T, U>`
                - This results to an error because the type parameter U of the base class has no value passed to it by the derived class.
                - `Node` class should contain a type parameter that will be equivalent to type parameter `U` of the base class.
    - When a generic class inherits from open constructed types, constraints on the derived class can either:
        - Cover a broad set of types (superset); or
        - At least satisfy the constraints set by the base type
        - Example:

            ```csharp
            class NodeItem<T> where T : System.IComparable<T>, new() { }
            class SpecialNodeItem<T> : NodeItem<T> where T : System.IComparable<T>, new() { }
            ```
            
    - Generic classes are [invariant](https://www.notion.so/Covariance-and-contravariance-79adacffea404a359d389eaebf0ecab6?pvs=21).    
        - For example, a method parameter that has the type of a generic class that accepts a class cannot accept a value that has a generic type with type argument that is only the derived class of the class used to define the generic class method parameter.

## Generic Interfaces

- A `DC.Lab` branch was created to contain the demo code in this section, named `generic-interfaces`
- A nested class inside a generic class has access to the type parameters defined in the generic class.
- Generic class inheritance rules also apply to interface inheritance.
- Starting C# 11, interfaces can declare `static abstract` and `static virtual` members.
    - Interfaces that declare either `static abstract` and `static virtual` are almost generic interfaces.

## Generic Method

- You cannot use a generic method with the same type parameter as that of the generic class, as it will return an exception.
    - Instead use a different type parameter for the generic method; or
    - Use the type parameter on a method parameter.
- Generic methods can be overloaded as long as different sets of type parameters are used.
    - Example:

```csharp
void DoWork();
void DoWork<T>();
void DoWork<T, U>();
```

## Generics and Arrays

- Single dimensional arrays that start with index 0 implements `IList<T>`
- `IList<T>`
    - Cannot be used to add or remove elements
    - Primarily used to retrieve array elements.

## Generics in run time

- The way C# generics behave in runtime differ for value types and reference types.
    - For value types, C# generates code that is unique to the value type provided.
        - For example, when a generic class is declared with an int type parameter, then the compiler will generate specialized code catering to the specific type parameter, int.
        - When a new generic class was initialized with a long type parameter, compiler will generate a new specialized code catering only to long type.
    - For reference type, C# will generate a specialized code for a single time.
        - Reference types will just reuse this generalized code as the substitute type parameter value contains object reference which makes it easy to reference other object types when needed.