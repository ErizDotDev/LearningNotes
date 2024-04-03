# 🏓 Reflection and attributes

---

## Overview

### **Attributes**

- A C# feature that allows metadata to be tagged on a piece of code, from something as small as a method, or a class, or something as huge as an entire assembly.
    - Metadata is information about the types defined in a program.

- After an attribute is applied, the attribute can then be queried at runtime with a technique called reflection.

- Have the following properties:
    - Adds metadata to your program.
    - Can be applied an unlimited number of times to specific areas of code (methods, class, etc.)
    - Can accept arguments the same way as methods.
    - The program can examine its own metadata or metadata in other programs using reflection.

- Class that defines attributes should be suffixed with the word Attribute to set itself apart from the other files in the solution.
- Attribute targets are usually the entity that the attribute applies itself to. But, it can also be applied explicitly using the below syntax:

```csharp
[assembly: AssemblyTitleAttribute()]
where:
LHS = code entity (class, module, assembly) that the attribute will apply itself to.
RHS = attribute that will be applied.
```

### **Reflection**

- Useful in the following scenarios:
    - Accessing attributes in your program’s metadata.
    - Examining and instantiating types in an assembly.
    - Building new types at runtime.
    - Performing late binding.

### Create custom attributes
---

- Define an attribute class that inherits from `System.Attribute`
- Any private properties defined in the attribute class are positional parameter.
- Meanwhile, the public properties are named parameters.
- `AttributeUsage` is used to declare where the attribute class can be applied to.

```csharp
[AttributeUsage(AttributeTargets.Class | AttributeTargets.Struct)]
public class AuthorAttribute: Attribute
```

- `AttributeUsage` has a property called `AllowMultiple` where when set to true, will allow the attribute to be applied multiple times to a single entity.
    - By default, an attribute can only be applied once to that entity.

### Access attributes using reflection
---

- The key method for getting information from custom attributes is `GetCustomAttributes` which returns an array of objects that are the runtime equivalent of the source code attributes.
- Attribute initialization does not happen until the classes with attributes are queried.

### [[generics|Generics]] and attributes
---

- Attributes can be applied to generic types the same way as nongeneric types.
    - Attributes can only be applied to open generic types and closed constructed generic types.
        - **Open generic type** is one where none of the type arguments are specified. (i.e. `Dictionary<TKey, TValue>`)
        - **Closed constructed generic type** is one where all the type arguments are specified. (i.e. `Dictionary<string, object>`)
    - Attributes cannot be applied to partially constructed generic types.
        - **Partially constructed generic types** is where some but not all type arguments are specified.

- A generic attribute property cannot have an unknown type value, else it will return an error.

### Common runtime attributes
---

- Some examples from libraries outside the .NET Core library:
    - NUnit testing framework uses the `[Test]` and `TestFixture`
    - [ASP.NET](http://ASP.NET) MVC uses attributes like `[Authorize]` and such.

- Here are few notable attributes built into .NET Core class libraries:
    - `[Obsolete]` - Useful in providing declarative documentation in the codebase.
    - `[Conditional]` - Useful for removing method calls on target methods that contains these attributes.
        - This works such that a symbol or a string is attached to the attribute when applying it to a method for example.
        - If the compiler sees that the symbol is defined using the `#define` pragma, then it will include the associated method calls of that method on compilation.
    - `[CallerMemberName]` - An attribute used to inject the name of the method that is calling another method.
        - Reduces typo errors.