# 🏓 Covariance and contravariance

---

## Overview

- Covariance and contravariance enables implicit reference conversion of array types, delegate types and generic type arguments.
    - **Implicit reference conversion**
        - This occurs in an inheritance setup.
        - A base class can be instantiated with its derived class.
        - However the instantiated class that is converted into the base class needs to be explicitly casted to the derived class in order to use the members of the derived class.

- **Assignment compatibility**
    - An object of a more derived type can be assigned to an object of less derived type.
    - Example: Assigning a string value to an object value.
        - object is the base class (less derived type) where the string type (more derived type) is derived from.

- **Covariance**    
    - An object that is instantiated with a more derived type argument can be assigned to an object instantiated with a less derived type.
    - Example: An `IEnumerable<string>` instantiated with `List<string>` is assigned to an `IEnumerable<object>` type.

- **Contravariance**     ^5878ac
    - Reverse of covariance
    - Object instantiated with a less derived type is assigned to an object instantiated with a more derived type.
    - Example: Assigning an `Action<object>` delegate to an `Action<string>` type.

- Covariance and contravariance highly support method group setups:    
    - A delegate receives a method that will be invoked when the delegate is used.
    - The methods being assigned to the delegate are mostly overloaded methods or methods that share the same base type as the delegate’s argument type.
        - Example: An `Action<string>` delegate type can accept methods that have the following method signature:
            - `Method1(string)`
            - `Method1(string, string)`
    - The delegate can simply change the method that they’re pointed to as long as it satisfies the type argument of the delegate.
    - Covariance and contravariance enables programmers to assign to delegates methods that return more derived types or methods that accept parameters of less derived type.

- A generic interface or delegate is called **variant** if its generic parameters are declared covariant or contravariant.
- Opposite of variant is invariant meaning it is neither covariant nor contravariant.
- Only reference types can be variant type parameters. Using a value type as type parameter will make it an invariant type parameter.

## Variance in generic interfaces

- Any genetic interface connected to collections and LINQ have **covariant** type argument.
- Any generic interface related to comparison have **contravariant** type argument.
- Variance in generic interfaces is supported for **reference types** only.
- Classes that implement variant interfaces are still invariant.
    - You cannot instantiate a given variant type with another variant type.
        - Example: Instantiating a `List<object>` with `List<string>` - This will result in compiler error.

## Create variant generic interfaces

- To declare variant generic interfaces, use the `in` and `out` keywords for generic type parameters.
    - `in` and `out` parameters cannot be variant as value types do not support variance.

- Use the `out` keyword to declare a covariant generic type parameter. But it must follow these rules:
    - Type is used only as a method’s return type and not as a method argument type.
        - One exception to this rule is if the method has a contravariant generic delegate as a method parameter.
    - Type is not used as a generic constraint for the interface methods.

- Use the `in` keyword to declare a contravariant generic type parameter.
    - Type is only used as a method argument type and not a return type.
        - Opposite of that of the covariant variety.
    - Type can be used as a generic constraint.
    - The covariant rules flip basically for contravariant.

- It is possible to support both covariance and contravariance in the same interface although in different generic argument types.

### Extending variant generic interfaces

- When extending a variant generic interfaces, always include the `in` or `out` keyword to explicitly specify whether the derived interface will support variance.
    - The compiler will never infer the variance of derived interface based on the inherited interface’s variance.

- An interface can inherit from both interfaces that implement both covariance and contravariance on each interface.
    - But, the generic type argument of the derived interface must be invariant.

- A compiler error will be returned if a contravariant interface inherits from a covariant interface, and vice versa.

### Avoiding ambiguity

- When implementing variant generic interfaces, sometimes variance can lead to ambiguity and should be avoided.
    - Example: Declaring a class that implements two variant generic interfaces with different type arguments.
        - This is because it is not clear or specified which interface implementation will be chosen at run time.
        - Compiler will also not throw an error in case this scenario happens.

## Variance in delegates

- Similar to variant generic interfaces:
    - Covariant generic type parameters are defined by using the `out` keyword
        - Can only be used as a return type and not as type for method parameters.
    - Contravariant generic type parameters are defined by using the `in` keyword
        - Can only be used as a type for method parameters and not as return types.
    - It is also possible to implement the two in one generic delegate.
    - Variance for generic type parameters is only applied for **reference types only**.

- .NET generic delegates have variant generic type parameters.
    - `Action<T>`
    - `Func<T>`
    - `Predicate<T>`

- DO NOT combine variant delegates.