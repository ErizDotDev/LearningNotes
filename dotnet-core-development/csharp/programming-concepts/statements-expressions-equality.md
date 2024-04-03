# Statements, expressions and equality

---

## Statements

- Actions that a program takes
- The order in which statements are being executed in the program is called _flow of control_ or _flow of execution_.

### Types of statements

- **Declaration statements**
    - Declares variables with or without assignment.
    - Also covers constants declaration with necessary initialization.

- **Expression statements**
    - Variable assignment
    - Object creation with assignment
    - Method invocation

- **Empty statement**
    - Denoted by a single semi-colon.
    - Used in places where a statement is expected but no actions needs to be performed.

- **Embedded statements**
    - Iteration statements (for, foreach loops) and selection statements (if-else) are typically followed by embedded statements.
    - Set of statements, single or multiple, enclosed in curly brackets, `{}`.

## Expression-bodied members

- Allows developers to write readable and concise code because of its simple format.
- Useful for code that can be expressed in a single line.
- Follows the following format:

```csharp
member => expression
public void LogToConsole(string message) => Console.WriteLine(message);
public string CleanString(string text) => string.Trim();
```

- When using expression-bodied members in methods, the return keyword may not be used.
- Properties, both read-only and with both getter and setter can implement expression body.
    - Read-only properties are like methods in that it returns a private variable value or a computed value based on private class members.
    - Indexers, which function similarly to properties with the getter and setter, can also implement expression bodies the same way.

- Constructor can implement an expression body if the constructor can encapsulate all initialization code into a single line.
    - That single line of code can call the method that contains all the initialization logic for that object.
    - This pattern can also be applied to finalizers in that all cleanup logic can be contained in a single method, and the expression body will contain the method call.

## Equality and equality comparisons

### Equality comparisons

- **Equivalence** ^b320a0
    - Value equality
    - Values contained by two different variables are equal
    - More straightforward tests by simply using the `==` operator.
    - Value equality is not achieved when comparing two integers of same value but different base types, one integer is declared as `Int16`, the other integer is declared as `Int32`.

- **Identity**    
    - Reference equality
    - Two variables refer to the same object in the memory.
    - Can use `ReferenceEquals` to test for reference equality.

- Value types can never have a reference equality.    
    - When an instance of a value type is assigned to a variable, a copy of the value is made.
    - `ReferenceEquals` for two value type containing variables will always return false.
        - Each variable is boxed on separate object instance.

- Equality comparisons of floating point variables, both double and float types, are problematic because of the imprecision of floating point arithmetic on binary computers.
    

### How to define equality for a class or struct

- Records automatically implement [[statements-expressions-equality#^b320a0|equivalence]].
    - Use records if there is a use of comparing equalities often for a particular object.

- When creating a class or a struct, it is important to know if equivalence will be built into the object.
    - The decision will stem from the usage of the object whether it will require heavily comparing the properties it contains or the object should be stored into a collection.
    - The code that will check for equality can be defined by comparing all property and field values.

- Five guarantees of equivalence:
    - Reflexive property → `x.Equals(x)` returns `true`
    - Symmetric property → `x.Equals(y)` returns the same value as `y.Equals(x)`
    - Transitive property → if `x.Equals(y) && y.Equals(z)` is `true` then `x.Equals(z)` is also equals `true`
    - An equality expression will always evaluate to the same value as long as the values on the objects involved in the comparison are not changed.
    - Any non-null value is not equal to null.
        - An equality expression will throw an exception if one of the values involved in the comparison is null.

- A defined struct in your code already has an implementation of the `Object.Equals(object)` method because a struct inherits the `System.ValueType` override of the same method.    
    - The `Object.Equals(object)` method relies on reflection to check for all properties and fields and comparing their values.
        - This is slower compared to implementing a custom solution for the struct equality comparison.

- Implementing equality code can be different for structs and classes, but both require the same code pattern for a basic starter implementation
    - Override the virtual method `Object.Equals(object)`
    - Implement the `System.IEquatable<T>` with the created class/struct as the generic type parameter.
        - Define a strongly typed test for equality by implementing `IEquality<T>.Equals()`.
        - Don’t throw exceptions on the `Equals` method.
        - It is fundamentally important that the two variables being compared must have the same type at runtime.
    - Optional but recommended: Overload the `==` and the `!=` operators.
        - Override `Object.GetHashCode` so that the two objects that have value equality produces the same hash code.
    - Optional: Implement the `IComparable<T>` interface if the class needs support for the greater than, and less than operations.
        - Overload the `>=`, `>`, `<=` and `<` operators.

- For classes, the default implementation of `Object.Equals(object)` performs a reference equality check.
    - If the class does not overload the `==` and the `!=` operators, by default the operators will do a reference equality check.

### How to test for reference equality

- Constant strings will always display identity qualities, or will always pass reference equality checks.
    - Constant string values in the same assembly are always created in the same location in memory even though one or both of the constant strings are not assigned with a value.
    - After changing a value of a string, the value of the strings were no longer interned (stored in the same memory location) and will fail reference equality checks.
    - A string created in runtime (strings created using string builders) cannot be interned.

### Additional Notes

- Value types should always override `Equals()`
    - If a value type contains reference types inside it, it uses Reflection to go through each properties inside said reference type.
        - This approach is awful for performance.
- Reference types can override `Equals()` to check for value equality.

**Guidelines for reference type equality**

- Consider overriding `Equals` if the type represents a value.
- Consider overriding the equality operator as well following the first point.
    - Most reference types don’t override the equality operator even though they’ve overrode the `Equals` method.

- Do not override `Equals` on a mutable reference type.
    - Overriding `Equals` requires overriding the `GetHashCode` method
    - Hash code of an object can change during its lifetime especially when the values inside the object change.=

**Guidelines for value type equality**

- If a value type contains properties that are reference type, override the `Equals` method.
- Consider overriding the equality operator if it supports equality operator overload.
- Implement the `IEquality<T>` interface.
    - Calling the `IEquality<T>