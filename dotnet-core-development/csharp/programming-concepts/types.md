# Types

---

[[introduction#**Boxing/unboxing**|Boxing and unboxing]]

## Casting and type conversions

- **Type conversions**
    - Sometimes there is a need to copy a value into a variable or a method parameter that is of another type.
        - Example 1: An integer variable will need to be passed into a method that accepts a double value.
        - Example 2: Assigning a class variable to an interface type.

    - **Conversion types:**
        - **Implicit conversion**
            - No special syntax required and the conversion is guaranteed to succeed.
            - Examples include conversions from smaller to larger integral types (from int to long) and conversion of derived class to base class.

        - **Explicit conversion**
            - Requires a cast expression, when information might get lost in the conversion.
            - Examples include numeric conversion to a type that has less precision or smaller and conversion of base class to derived class.
            - Casting between reference types does not change the runtime type of the underlying object; It is simply changing how the value is referenced or labeled to fit that particular object.

        - **User defined conversion**
        - **Conversion with helper classes**

            - Used to convert between non-compatible types (int to `DateTime` for example)
            - Built in .NET methods like `Parse` can help here.

**Type conversion exceptions at runtime**

- It is important that objects are checked if they are compatible to be casted to a particular type.
    - The `is` operator is the most common and easiest way to achieve that.

## How to convert between hexadecimal strings and numeric types

- The hexadecimal code for a letter can be extracted by converting a string into a char array which contains the hexadecimal equivalent of each character in the string.
- A hex code can be converted back to a letter with the following ways:
    - Using `Convert.ToInt32(hexCode, 16)`
        - where 16 stands for the hexadecimal mode for conversion
    - Using `Char.ConvertFromUtf32()`
    - Using `Int32.Parse(hexCode, System.Globalization.NumberStyles.HexNumber)`