# 🚨 Enumerations
---

- We change the underlying type of an enum in scenarios where a .NET application will be deployed in a low memory device.
	- By default, enums are of type int.
	- Make sure that the enum values fit the available values of the underlying type.
- Under the hood, enums provide explicit implementations of the `IConvertible` interface for converting from enumeration value to integral type.

## Best practices

- Create a constant that will represent the 0 value, even if it is not needed.
    - CLR initializes the enum in memory with a value of 0, which will become an illegal value if it is not used.
    - It is also to let other people that will read the code to know that the value of the enum is anything but zero.

- Do not create a non-zero constant if it will not be used, to avoid confusion.
- Consider validating the enum if it comes in as a method parameter.
    - It is possible for an enum variable to be assigned with a value that is not part of the enumeration by casting a value not included in the enum as the enum type which will be converted to whatever in the enum. ^98512e

---

## Performing operations

- There are a couple of ways to convert the integral type to an enumeration type and then back to an integral type.
    - Through type casting
    - Through `Enum.ToObject`
        - Still needs casting to successfully convert to an enum type because the return type of the method is `object`.
    - By using `Convert.ChangeType` coupled with `Enum.GetUnderlyingType`
        - In this approach, it is not required for the unknown type to be known at compile time.

- Given this [[enums#^98512e|statement]], use `Enum.IsDefined` to check if the value is a part of the enumeration values.
- Use `Enum.Parse` and `Enum.TryParse` to parse the value or string representation of enum type
    - It is possible to get the enum value of a string if the entire string value equates to a string that is used to define the constant.

- You can format enums by:
    - Calling `Enum.Format`
    - Calling the `ToString` method of the enum with the format parameter:
        - D - int value;
        - X - hexadecimal value;
        - G - string value (if possible), else integer value
        - F - string value (if possible) displays the summation of entries (all possible enum values)

- You can iterate the numbers by:
    - Using `GetNames` to enumerate the constants in the enum and use `Parse` to get the underlying values
    - Using `GetValues` to enumerate the values in the enum and use `ToObject` to get the string representation.

---

## Flags attribute

- Primarily used for entities or variables that expects multiple enum values.
- Allows expressions of having multiple enums being assigned to a variable.
    - Enums in this expression are known as bit fields, because they can use bitwise operators (mostly OR (|)) to inform the user that multiple enum values can be accepted.

### Best practice

- Use the Flags attribute only when bitwise operators are used on the numeric value.
- Define enumeration constants in powers of two (1, 2, 4, 8...) to avoid overlapping values
    - Check out this [link](https://learn.microsoft.com/en-us/dotnet/fundamentals/runtime-libraries/system-flagsattribute#examples) to see why defining flag enumerations in power of two is a must.
    - Basically the blank numbers indicate the amount of combinations possible for a set of the preceding elements.
        - 3 represents a combination of enum items 1 and 2
        - 5 represents a combination of 1 and 4
        - 6 represents a combination of 2 and 4, and so on...

- Use the Flags attribute for enums that expects a combination of values from its very own enum values.
    - Example: Enums that have Read and Write value should also have a ReadWrite constant and needs a flag to implement this type of constant.

- Be careful of using negative enum values with flags as this can cause confusion in your code and may result to incorrect results.

- Validate if a flag has been assigned with a numeric value using the `HasFlag` method.
    - Using the `HasFlag` method is like performing a bitwise AND operator.

- Same as base enum practice, add constant for zero values.
    - This zero value cannot be used in a bitwise operation
        - Use logical operators instead.