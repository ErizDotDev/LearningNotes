# Types and members

### **Class**

- contains state (fields) and actions (methods)

### Enums

- An enum element can be defined where it is a certain combination of multiple enum values.
    - The enum values are separated by the pipe `|` symbol.

```csharp
[Flags]
public enum Seasons
{
    None = 0,
    Summer = 1,
    Autumn = 2,
    Winter = 4,
    Spring = 8,
    All = Summer | Autumn | Winter | Spring
}
```

- You can define bit fields by using `FlagsAttribute`.
    - Bit fields enable the use of bitwise operators.
- Enumerations are used to identify unique elements (e.g. Days of the week, Country, Region Names)
- Bit fields are used to identify elements that might occur in combination

### **Reference Types**

- Immediately accesses the value of the variable that was passed into the method.
- Returning a reference value that was passed into the method is unnecessary, because the changes to the reference value will also affect the value in the caller method.
- Always allocated in the heap, and can be accessed anywhere.
- Null value is assigned to a reference type variable upon declaration
- Static objects are classified as reference types.

### **Value Types**

- Can be allocated in the heap or the stack depending on where they are declared.
    - As a variable inside a method → stack
    - As a method parameter → stack
    - As a member of a class → heap
    - As a member of a struct → wherever the struct is located
- Behind the scenes:
    - The CLR supplies a boxed type that corresponds to the value type.
        - Essentially, it is a class that has the same state and behavior as the value type.
    - Instance of a value type is boxed when it is passed to a method that accepts a parameter of type `System.Object`

### **readonly vs. const**

**readonly**

- A readonly variable can be assigned later.
- Can be used in more complex types.
- Evaluated at run time.

**const**

- A const variable should be assigned immediately during assignment, and cannot be changed after.
- Can only be used with simple types.
- Implicitly public and must be declared within a class or struct.
- Evaluated at compile time.

### **Using the ref keyword**

- Using the ref keyword for a value type in a method signature indicates that a value may be modified inside a method.
- For a reference type, this indicates that you can change the reference where the referred reference type points to, to a new location.

**Reference**

[https://dev.to/tyrrrz/interview-question-heap-vs-stack-c-5aae](https://dev.to/tyrrrz/interview-question-heap-vs-stack-c-5aae)