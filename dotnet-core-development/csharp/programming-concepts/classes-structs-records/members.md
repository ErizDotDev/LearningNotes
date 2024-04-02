# Members

---

- Private members in base classes are inherited by derived classes but are not accessible from derived classes.

## Abstract and sealed classes and class members

- `abstract` keyword enables the creation of incomplete classes and class members.
    - The implementation shall be provided by the derived classes.
- `sealed` keyword enables the prevention of the inheritance of a class or class members that were previously marked by the `virtual` keyword.
    - Because it prevents inheritance, sealed classes cannot be a base class, or an abstract class.

### Abstract classes and members

- Abstract class cannot be instantiated.
- Functions similarly to an interface in that it gives developers an idea of what methods are going to be implemented in a class that inherits from the abstract class.

- Abstract classes can also contain abstract methods.
    - This completes the analogy of an abstract class and interface

- Given the scenario where an abstract class E inherits from a normal class D, and class D has a virtual method called `DoWork()`.
    - Class E can override the `DoWork()`
    - Class E can also place an `abstract` keyword to the `DoWork()` method.
        - What this will do can be exhibited when a new class, class F, implements class E.
        - When class F overrides the `DoWork()` method in class E, class F ignores class D’s implementation.
        - This pattern allows the forcing of derived classes to provide new implementations for virtual methods.

### Sealed classes and members

- Because it cannot be used as a base class, some run time optimizations can make calling sealed classes faster.
- To negate the virtual nature of a derived class member, add `sealed` keyword before the `override` keyword.

---

## Static classes and members

- Static classes are used as a convenient container for sets of methods that operates solely on input parameters and does not have to get or set any internal instance fields.
    - Example: Utility math libraries
        - There is no requirement to store or retrieve data in these libraries

- Static classes:
    - Can contain only static members
    - Cannot be instantiated
    - Sealed
    - Cannot contain instance constructors

- Behind the scenes:
    - Like all class types, a static class' type information is loaded by the .NET runtime when the program that references the class is loaded.
    - It is not specified when the class is loaded but it is guaranteed that the program will load the class, have its fields initialized, and that static constructor called before the class is referenced for the first time in the program.
    - Static constructor is called only one time
    - Static class remains in the memory for the lifetime of the application domain in which your program resides.

### Static members

- A non-static class can contain static members
    - A static member of a non-static class can be called even without instantiating the class.
    - Only one copy of a static member exists regardless of how many objects are instantiated using the class.

- Static methods cannot access non-static members
    - They also cannot access an instance variable of any object unless it is specified in the method parameter.

- Uses of static fields:
    - Keep count of number of objects that are instantiated.
    - Store a value that must be shared by all instances.

- A `const` field is essentially a static field in behavior.
- If a class contains static fields, provide a static constructor, if needed, to initialize these fields when the class is loaded.

- Performance
    - A static method call generates a call instruction to MSIL (MS Intermediate Language)
    - An instance method call generates a `callvirt instruction` which also checks for null object references.
    - The performance difference between the two, most of the time, is insignificant.

---

## Access Modifiers

[[program-building-blocks#Accessibility|Modifiers]]

| public                           | internal | private | protected | protected internal | private protected | Default |          |
| -------------------------------- | -------- | ------- | --------- | ------------------ | ----------------- | ------- | -------- |
| Unnested classes/records/structs | ✅        | ✅       | ❌         | ❌                  | ❌                 | ❌       |          |
| Struct members [1]               | ✅        | ✅       | ✅         | ❌                  | ❌                 | ❌       | private  |
| Class members [1]                | ✅        | ✅       | ✅         | ✅                  | ✅                 | ✅       | private  |
| Interfaces                       | ✅        | ✅       | ❌         | ❌                  | ❌                 | ❌       | internal |
| Enums                            | ✅        | ❌       | ❌         | ❌                  | ❌                 | ❌       |          |

[1] - Including nested structs/classes

- Derived classes and records can’t have greater accessibility than their base types.
- It is possible to enable other assemblies to access internal types by using the `InternalsVisibleToAttribute` attribute.
- Struct members can’t be declared on the above modifiers because structs don’t support inheritance.
- User defined operators can only be `public` and `static`
    

---

## Fields

**Rules**

- Declare `private` or `protected` on class fields.
    - Data that your class exposes to outside code, or code outside a given class, should be provided through methods, properties and indexers.
        - These constructs, or other class members, can guard against invalid values.

- `public` field can be declared but the downside is you cannot protect the field from being assigned with invalid values by the client code.    
- Fields should be declared when it was going to be used by more than one method in a class.
    - Variables that is only used by one method should only be a local variable, or a variable declared in a method.

- Fields are initialized immediately before the constructor of the class is called to be initialized.    
    - If the constructor assigns the value of the field, it will overwritten any value assigned beforehand.

- A field can be `readonly` - a field that can only be assigned a value during initialization or in a constructor.
- A `static readonly` field is similar to a constant, but the compiler does not have access to the value of this field at compile time, only during runtime.
- A field can be `required` - this field must be initialized in the constructor.
    - A `required` field cannot be a `readonly` field.

- In C# 12, fields can be replaced by primary constructor parameters.
    - When a primary constructor is defined along with all its parameters, similar to defining a regular constructor, the parameters automatically becomes a field.
    - The primary constructor parameters becomes available in the entire scope of the class which eliminates the need of defining a regular field.

---

## Constants

- User-defined types cannot be constants, only built-in C# types can be constants.
    - Use `readonly` modifier for user defined types, instead.
- Enum types enables the definition of named constants for built-in integral types(`int`, `long`, etc.)
- The compiler, behind the scenes, immediately substitutes the constant with the literal value in the resulting Intermediate Language (IL) code.

---

## Abstract properties

- Same as abstract classes and methods, a class does not need to define an implementation for an abstract property.
    - The derived class has the responsibility to define the implementation of the abstract property.
- The implementation of the abstract property in the derived class carries the keyword `override`.
- A sample code is available on the `CSharp.Lab` solution in branch `abstract-properties`.