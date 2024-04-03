# Major language areas

### **Arrays, collections, and LINQ**

- `System.Span<T>` vs. `System.Memory<T>`
    - `System.Span<T>` accesses continuous memory on the stack;
    - `System.Memory<T>` accesses continuous memory on the heap.

**Arrays**

- Once an array is initialized with the new keyword, its values will be initialized with a default value of 0 for numeric types and null for reference types.
- To get the total number of elements in a multi-dimensional array, you multiple the length of each array dimensions.

### String Interpolation

- Another way of formatting a Date in a string interpolation context

```csharp
Console.WriteLine($"The date today is: {weatherData.Date:MM-dd-yyyy}");
```

### [[delegates|Delegates]] and lambda expressions

- Delegates enables functions to become variables and be passed into methods.
- To pass in a callback function, we use the type `Function`.
- Lambda expression - anonymous function, or inline functions, that are created when declared. ^3a95ec

### Attributes

- Provide metadata for code
- Use case → add labels to class properties in order to identify certain piece of functionality/information regarding a certain value or field. Think Entity Framework
- Good use case for an attribute → [https://levelup.gitconnected.com/practical-c-attributes-for-beginners-and-intermediate-developers-cf916bfd1194](https://levelup.gitconnected.com/practical-c-attributes-for-beginners-and-intermediate-developers-cf916bfd1194)
- Additional reference: [https://www.tutorialspoint.com/csharp/csharp_attributes.htm](https://www.tutorialspoint.com/csharp/csharp_attributes.htm)