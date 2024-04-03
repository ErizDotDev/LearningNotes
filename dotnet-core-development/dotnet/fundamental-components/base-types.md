# 🚨 Base types
---

## Language Independence

- Applications written in languages that target .NET implementations like C#, F# and Visual Basic can access types and libraries developed for .NET without caring what language that type or library is written in.
- To utilize this feature, code must be written such that it is compliant to the CLS (_Common Language Specification_)
- To validate that the code written is CLS compliant, add the `CLSCompliantAttribute` to your source code.

- Writing CLS compliant code means that only certain types and language features are allowed to be used.
    - For example, when defining an Age property, the type `UInt16` can’t be used because it is not CLS compliant - other languages that are CLS compliant may not have a type that corresponds to the `UInt16` type. Instead, use the `Int16` type.

- Some compilers already enforces CLS compliance rules when compiling source code, particularly in C#.

### **Simple CLS compliant code rules**

- A CLS compliant derived type should have a base class that is also CLS compliant.
- All types that appear in a method signature (parameters and return types) must be CLS compliant.
    - To resolve issues where a method signature or other object members have types that are not CLS compliant:
        - Replace the non CLS compliant types with compliant types;
        - Handle previous type behaviors in code.

- For generic types:
    - All types used to instantiate a generic type must be CLS compliant.
    - All types used as constraints in generic parameters must be CLS complaint.

- An object name is not CLS compliant if it is a lower case variant of another existing object in the same assembly.
- Characters that have multiple encoding representations are not CLS compliant.

- _Arrays_
    - Defining an array with a lower bound of one is not CLS compliant.
        - Compiler does not detect this.
    - Array elements must consist of CLS compliant types.
- 
- _Interfaces_
    - Should not have a static method or member.
    - Should not have any fields.
    - Should not have any methods that are non CLS compliant.

- _Enums_
    - Should have an underlying type that is a CLS compliant type.

- Global static fields and methods are not CLS compliant.

- _Constructors_
    - Constructor of a derived class must call the constructor of its base class before it can access inherited instance data.

- _Exceptions_
    - Exceptions must inherit from `System.Exception` class.

- _Attributes_
    - Attributes must inherit from `System.Attribute` class.
    - Using an attribute that has a property that is not CLS compliant will still compile successfully.

### **`CLSCompliantAttribute` attribute**

- Used to indicate whether a program element complies with CLS.
- Can be used:
    - To define parts of public interface that are and aren’t CLS-compliant.
        - Those parts that are CLS compliant are guaranteed to be accessed by other languages and tools that target .NET.
    - To ensure that the library’s public interface only program elements that are CLS compliant.

- Only apply this attribute to assemblies, types and type members.
- If an assembly is marked as CLS compliant, then the type it contains are also CLS compliant.

- Explicitly specify that a program element is non CLS compliant by using the `CLSCompliantAttribute` with `isCompliant` value set to `false`.
    - A non-compliant type cannot have a compliant member.

### Notes

- If there are any rules that I may have not noted above, it is because these rules are already enforced in the compiler and that we are innately trained to write code in such a way.
- You can use C# reserved keywords as type names on types from a CLS compliant language that allows the usage of said keywords as type names by adding the `@` symbol at the start of the type name.

### Sample Code

- `dotnet/language-independence/events`
- `dotnet/language-independence/generic-types`

---

## Type Conversion

- **Implicit conversion**
    - “Widening” conversion
    - No loss of data but can have loss of precision

- **Explicit conversion** - “Narrowing” conversion
    - “Narrowing” conversion
    - Potential loss of data.

    - Can produce different results.
        - Example: When converting a double value to an integer value, using the `Convert.ToInt32()` approach rounds up the resulting value, while using the `(int)` approach rounds down the resulting value because it truncates the value.
- 
    - Can be done in a checked or unchecked manner.
        - Checked explicit conversion returns an `OverflowException` if the value goes over the range of the acceptable values for that type.
        - Unchecked explicit conversion does not return an exception, though this may result in an undefined or incorrect value.
            - This is the default option in C#.

### `IConvertible` interface

- Interface used to support the conversion of any type to a common language runtime base type (Boolean, Byte, Char, DateTime, Int32, etc…)
- An expensive operation to call and make.

### Convert class

- The recommended way to convert from one base to another.
- Accepts the following parameters:
    - **Number system**
        - When converting a whole number to a non-decimal string, you can specify which number system that value will be converted in.
        - Can be binary, octal or hexadecimal

    - **`IFormatProvider`**
        - Supplies culture specific information to help in conversion process.
        - If the value of `IFormatProvider` is `null` it will use whatever the current culture is.
        - Only works with the following conversions:
            - To numeric type
            - To date and time
            - To string

### Other conversions

- Converting primitive numeric types into byte array;
- Character encoding and decoding;

### Sample Code

- `dotnet/type-conversion/explicit`
- `dotnet/type-conversion/implicit`
- `dotnet/type-conversion/other-approaches`

---

## Choosing between anonymous and tuple types

- Declaring `ValueTuple`

```csharp
var dates = new[]
{
    DateTime.UtcNow.AddHours(-1),
    DateTime.UtcNow,
    DateTime.UtcNow.AddHours(1)
};

foreach (var (formatted, ticks) in 
    dates.Select(date =>
        (Formatted: $"{date:MMM dd, yyyy at hh:mm zzz}", date.Ticks)))
{
    Console.WriteLine($"Ticks: {ticks}, formatted: {formatted}");
}
```

- Use `ValueTuple` over `Tuple` and anonymous types.
    - `ValueTuple` support custom property names like anonymous types.
    - `ValueTuple` also has deconstruction support, the only one of the three that has it.

- Think about the following tradeoffs:
    - `ValueTuple` is mutable, `Tuple` is read-only.
    - Anonymous types can be used in expression trees, while tuples cannot.

- Prioritize using the following in order: `ValueTuple` > anonymous types > `Tuple`

### Anonymous types

- Behind the scenes, if two or more anonymous objects exist in the same assembly and they both have the same names and types in the same order, the compiler considers it as the same type and they’ll share the same compiler-generated type information.

### Notes

- Decimal has fewer significant digits compared to the double type.

---

## `System.Object` class

### `Finalize` method

- Performs cleanup operations on an object’s unhandled resources before it destroys said object.
- Method is protected and can only be accessed through the class or its derived classes.

**Behind the scenes:**
- Object class has no implementation for the `Finalize` method.
- GC does not mark types derived from `Object` for finalization until a type overrides the `Finalize` method.
    
- When a type overrides the `Finalize` method:
    
    - GC adds an entry for each type’s instance to the _finalization queue_ - a structure that contains all objects in heap whose finalization code must run before the GC get their memory.
    - GC calls the `Finalize` method automatically under the following conditions:
        - After GC discovers that an object is already inaccessible, unless that object is exempted from finalization by using `GC.SuppressFinalize()` method.
        - Applicable only for .NET Framework, during application or domain shutdown.
            - Even accessible objects are still finalized.

- _Limitations:_    
    - Finalizer has no fixed time when it executes.
        - To ensure that you can predict when the resources are released, implement a `Close` method or provide a `IDisposable.Dispose` implementation.
    - Finalizer is not guaranteed to run in order for two related objects.
        - If `ObjectA` is inherited by `ObjectB` it is possible that `ObjectA` is finalized first before `ObjectB`.
    - It is unspecified which thread the finalizer will run.

- The `Finalize` method may not be completed or be executed because of the following:    
    - If another finalizer blocks indefinitely (due to endless loop, object lock issue)
    - If the process terminates before the runtime’s cleanup operation is started.

- If `Finalize` throws an exception and no proper exception handling is in place to handle this, the runtime will terminate the process and no code residing inside a `try/finally` or finalizers will be executed.
    

**Override the `Finalize` method**
- Override only for classes that uses unmanaged resources like database connections that must be released when the object that uses them is discarded during garbage collection.
- `Finalize` should not be implemented for managed objects because GC will dispose them automatically.

- Does nothing by default so only use them when absolutely necessary. (See first point)
    - Reclaiming memory becomes longer and expensive when a finalizing operation occurs since it will require two garbage collections.
    - Override `Finalize` only for reference types.
        - It ignores finalizers on value types.

- Default scope of `Finalize` is `protected`
    - Maintain this scope when overriding this method.
    - Prevent application users from calling `Finalize` directly.

- All implementation of `Finalize` on derived types must only call the base type’s implementation of `Finalize` and it can never call another object’s implementation of `Finalize`.
    - Objects being called can be collected at the same time as the calling object which is the case for when a .NET application was terminated.

- **IMPORTANT NOTE**: C# compiler will not allow you to override the `Finalize` method. Instead, you provide a finalizer by implementing a class destructor.
    
- We do not know when the GC performs finalization.    
    - To release resources immediately, use the dispose pattern together with the `IDisposable` interface.
    - `IDisposable.Dispose` will be called first to try to clear out unmanaged resources and the `Finalize` method will be called next if there are remaining unmanaged resources.

- `Finalize` can take on any action, including resurrecting an object. (making it accessible again)
    - Object can only be resurrected once.

**`SafeHandle` alternative**
- Due to the complexity of creating reliable finalizers, you can use an object derived from `System.Runtime.InteropServices.SafeHandle` class to wrap unmanaged resources and then implement the `Dispose` pattern without a finalizer.

### `GetHashCode` method

- Provides hash code to objects that algorithms use for equality checks
    - A _hash code_ is a numeric value used to insert and identify an object in hash-based collections like `Dictionary<TKey, TValue`, and `HashTable`
        - Hash codes do not have a permanent value so do not use it on normal use cases. (Example: storing it in a database, retrieving value from a dictionary)

    - Two objects that are equal return hash codes that are equal, but two hash codes that are equal does not mean the objects are equal.
        - Different or unequal objects can still have identical hash codes.

- .NET recommends that you do not use the default implement of `GetHashCode` to get object identifiers for hashing purposes.    
    - `GetHashCode` implementations may differ from the different .NET versions.
    - Because of this:
        - Do not assume that equal hash codes equals equal objects
        - Never use hash code outside the application domain where it was created
            - The same object may hash or may have different hash value across application domains.

- This method is overridable.
    - If not overridden, it will exhibit the following behavior in each type categories:
        - For reference types, it will compute a hash based on an object’s reference.
        - For value types, it will use reflection to compute a hash code based on the values of the type’s field.
    - If this method is overridden, the `Equals` method should be overridden as well.

- Hash code can be generated with a mixture of different class/struct properties and combining them together using the XOR operator `^`.    
    - Problematic because there is a tendency that with different prop values it will still produce similar hash code.
        - One solution here is to use a `Tuple` object to generate the hash code as a `Tuple` object will respect the order of each value.

- **Sample Code:**    
    - `dotnet/object-class/gethashcode`

### `ToString` method

- The default implementation of this method returns the full name of the type.
    - A type’s full name is comprised of the namespace where a specific type or class belongs, then its class or type name.

- Can be overridden to provide a custom way for an object to be displayed as a string.
    - This is especially true for collections and arrays.

- Also usually overridden to provide support for string format and formatting based on culture.

- Several options to generate string from arrays or collections    
    - Use `foreach` to loop through each element and generate the string result
    - Create a wrapper class for C# collections and override the `ToString()` method.
    - Create an extension method - A `ToString()` method that has a parameter
        - Original implementation of `ToString()` is parameterless and cannot be overridden in an extension method.

- **Sample Code:**
    - `dotnet/object-class/tostring`