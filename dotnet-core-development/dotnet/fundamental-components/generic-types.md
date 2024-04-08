# ⭐ Generic Types
---

[[generics|Read more information about generics here.]]

## Overview

### Limitations

- Does not support context-bound generic types.
    - Context-bound objects are challenging to work with.
        - Context-bound objects are objects derived from `ContextBoundObject` class. You can read more about them [here](https://www.notion.so/Miscellaneous-fa65dfdc3bef4994adefa553f0f6c6f3?pvs=21).
    - Coupling with generics it will break the simplicity of generics, which is why maintainers of C# decided to not pursue this design route.

- Enums cannot have generic type parameters.
    - They are numeric by nature.
    - Generics are resolved at runtime whereas enums are compile-time constants.

- Dynamic methods cannot be generic.
    - Dynamic methods have unknown types by compile time and are resolved at runtime.
    - This can cause confusion in code.

- When initializing a nested type whose enclosing type is a generic and has a generic type parameter, a type parameter should always be provided for the enclosing generic type.

---

## Generic delegates to manipulate arrays and lists

- `Action<T>`
- `Predicate<T>`

### `Comparison<T>`

- Delegate used in `Array.Sort()` or `List.Sort()` functions.
- This delegate should return an int with `T` being the type of the item being compared.
    - With input parameters `x` and `y`, the function should return the following values:
        - -1 (Less than 0 - `x` is less than `y`)
        - 0 (Equal to 0 - `x` is equal to `y`)
        - 1 (Greater than 0 - `x` is greater than `y`)

`Converter<TInput, TOutput>`

- Used to convert array elements from one type to another.
- The type parameters represent the type which will be involved in the conversion.
- Partnered with `Array.ConvertAll` method.

---

## Generic Math

- Recent .NET and C# versions introduced generic mathematical functions and interfaces.
- This is beneficial for library creators who do mathematical calculations.
- Now, overloaded methods that accept different numerical types as parameters are unnecessary and obsolete as generic mathematical methods are introduced that accept numeric type parameters.
- Important to take note of the following interfaces:
    - `INumber<TSelf>`
    - `IAdditionOperators<TSelf, TOTher, TResult>`
    - `IEqualityOperators<TSelf, TOther, TResult>`
    - and many more...

---

## Covariance and contravariance

[[contravariance-covariance|Read here to get initial information about topic.]]

- Variant type parameters are restricted only for delegates and interfaces.
- As a general rule, a covariant type parameter can be used as a return type for a delegate and the contravariant type parameter can be used as a parameter type.
- For interfaces, a contravariant type parameter can be used for the interface’s methods and the covariant type parameter can be used as parameter type.

---

## Generic interfaces

### Static abstract and virtual members

- Applicable only for generic interfaces.
- Useful for mathematical code where operator needs to be overloaded, or have a custom implementation.
- Also useful for adding metadata to a class and having these metadata accessed on a method in a generic class implementing a generic interface.
- Check out `DC.Lab` branch `csharp/static-abstract-members` for a simple demonstration of static abstract members.
- Difference between static abstract and static virtual members:
    - Static abstract members do not need implementation, static virtual members need implementation.