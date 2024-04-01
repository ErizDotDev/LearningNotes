# Fundamentals

## Program Structure

### Main and command line arguments

- The Main method can be declared with or without the string[] `args` parameter. Because of this you can use the `GetCommandLineArguments()` to get the command line arguments.
- It is recommended to return an integer value for console applications, as it lets other programs know the status of the application after it was executed.
- The main class can be enclosed or contained in another class so long as it can be discovered by the compiler.

### Top-level statements

- Classes and namespaces can be declared in a top level statement but they should come **AFTER** the top-level statements.
- `args` can be called as well from top-level statements, as they are not explicitly defined.
- Treat a top-level statement as writing statements that goes inside the Main method. With the exception of the first bullet point, write top level statements as normally as possible. You can also do a return of an integer value if you are returning execution status codes.

---

## Type system

### **Type Conversions**

- _Implicit conversion_ - an example of this is when you want to convert int to long (small to large numeric value)
    
- _Explicit conversion_ - follows the syntax below
    
    ```csharp
    double a = 1234.56;
    int b = (int)a;
    ```
    
- For reference types, casting a derived class to base class is fine, called “*upcasting*”.
    
- However, casting a base class to derived class, known as “*downcasting*”, can be tricky:
    
    - Properties of the derived class may not be available in base class, and properties in the base class may not exist in the derived class, and casting between the two will cause an issue where values get lost.
    - In order to downcast successfully, the runtime type of the object to be casted should be the derived class.

### Types of literal values

- Append the following characters at the end of the literal values to give the compiler a clue which type is being assigned to that literal value.
    - `f` - float
    - `d` - double
    - `m` - decimal