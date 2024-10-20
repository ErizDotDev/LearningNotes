# 🏓 Fundamentals

## Program Structure

### Main and command line arguments

- The Main method can be declared with or without the string[] `args` parameter. Because of this you can use the `GetCommandLineArguments()` to get the command line arguments.
- It is recommended to return an integer value for console applications, as it lets other programs know the status of the application after it was executed.
- The main class can be enclosed or contained in another class so long as it can be discovered by the compiler.

- A Main method without a return type implicitly returns a value of 0.
	- C# programs are consistent with C based programs where a program needs to return an integer value to indicate that a program ran successfully, with value of 0.

### Top-level statements

- Classes and namespaces can be declared in a top level statement but they should come **AFTER** the top-level statements.
- `args` can be called as well from top-level statements, as they are not explicitly defined.
- Treat a top-level statement as writing statements that goes inside the Main method. With the exception of the first bullet point, write top level statements as normally as possible. You can also do a return of an integer value if you are returning execution status codes.

- Some rules:
	- Only one file can contain top level statements.
	- When using top level statements, program should not declare an entry point (aka Main method)
	- Top level statements can still access array of string arguments.
	- Top level statements can still return an integer value.
	- Methods declared in top level statements are considered as local methods to the Main method.

---

## Type system

- Alternatives to `DateTime` and `TimeSpan` classes are the structs `DateOnly` and `TimeOnly` which are already available in the latest version of .NET.
- Use `BigInteger` to handle huge numeric values without thinking of upper and lower bound limits.
	- Use static methods or your regular arithmetic operators under this type to be able to manipulate or perform operations to these numeric values that cannot be hosted inside normal intrinsic types like `int` and `long`

### **Type Conversions**

- _Implicit conversion_ - an example of this is when you want to convert int to long (small to large numeric value)
    
- _Explicit conversion_ - follows the syntax below
    
    ```csharp
    double a = 1234.56;
    int b = (int)a;
    ```
    
- Use this only when you accept that there will be loss of data in your code when you are downcasting.
- For reference types, casting a derived class to base class is fine, called “*upcasting*”.
    
- However, casting a base class to derived class, known as “*downcasting*”, can be tricky: ^332b21
    - Properties of the derived class may not be available in base class, and properties in the base class may not exist in the derived class, and casting between the two will cause an issue where values get lost.
	    - This is also known as narrowing conversion.
    - In order to downcast successfully, the runtime type of the object to be casted should be the derived class.

### Types of literal values

- Refer to the chart below to know the default type assigned to a specific format of a numeric value.
	- 500 is automatically assigned as an `int`.
		- To specific 500 to be of type `long`, add `l` or `L` to 500, like `500L`
	- 510.23 is automatically assigned with type `double`
		- To change the type to `float`, suffix the literal numeric value with `F` or `f`
- Append the following characters at the end of the literal values to give the compiler a clue which type is being assigned to that literal value.
    - `f` - float
    - `d` - double
    - `m` - decimal
    - `l` - long

### `System.Char`

- Provides useful methods for checking if a character is a digit, a letter, a white space, or a punctuation.

### `checked` keyword

- When you add two byte values and its sum is greater than the maximum value for a byte which is 255, its resulting value will be the difference of the total sum of the two byte values and the maximum value allowed for bytes.
	- What happened to the resulting value is called an *overflow*.
	- Total byte values is 256 and not 255 because 0 is included in the count.

- The checked keyword allows you to verify if a specific mathematical operation will result in an overflow.
	- If overflow occurred, an `OverflowException` will be thrown.
- Usage:
	- Can be used like a method call.
		- `byte sum = checked((byte)Add(150, 200));`
	- Can also be used over a block of code (similar with lock statements)
		```csharp
		checked
		{
			byte sum = (byte)Add(150, 200);
			Console.WriteLine(sum);
		}
```

- In order to enforce the `checked` keyword functionality to all your mathematical operations across an entire project, use the `CheckForOverflowUnderflow` setting in the project file.

### `unchecked` keyword

- Use this only when there are parts of your code where overflows are acceptable.
	- When an overflow is encountered, it will not throw any exception.