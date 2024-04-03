# 🏓 Iterators

---

- For a sequence that is produced asynchronously, use `await foreach`.
- Code examples for iterators is available under the `csharp/iterators` branch of the `DC.Lab` project.

### Syntax information

- Iterator occurs as a method or a get accessor.
    - Iterators cannot occur in an event, instance and static constructor, and static finalizer.
- Iterator cannot have an `in`, `ref`, or `out` parameters.
- `yield` is not a reserved keyword and can only be properly used by placing it before a `return` or a `break` statement.

### Using iterators with generic list

- When implementing an `IEnumerable` interface, if the method should implement a generic `GetEnumerator()` method, the class should also implement a non-generic `GetEnumerator()` method.
    - The non-generic `GetEnumerator()` method will defer to the generic variant of the method.

### Enumeration sources with iterator methods

- _iterator methods_
    - a C# feature that provides a custom way to go over an array or collection.
    - characterized by `yield return` statements in the method.
        - A `yield return` statement will return an array or a collection’s elements one at a time.
        - Once a `yield return` statement is reached, the current code location is remembered.
            - The next time, the iterator method was called it will restart at the remembered line of code.
- You can have as many `yield return` as you can in the method.
- However, there cannot be a `yield return` and a `return` statement in the same method.
    - If a scenario arises where the logic may have a return and a `yield return`, create another method for the iterator.
- This provides another way of supplying data instead of storing it inside a list, or an array.
- A `yield break` statement will end the iteration.

### Deeper dive into `foreach`

- Under the hood, the foreach statement expands into a code pattern that uses the `IEnumerator<T>` and the `IEnumerable<T>` interfaces.
    
    - This minimizes developers’ mistakes of not properly managing resources.
    - Expands the code into something like this:
    
    ```csharp
    var enumerator = collection.GetEnumerator();
    
    try
    {
    	while (enumerator.MoveNext())
    	{
    		var item = enumerator.Current;
    		Console.WriteLine(item.ToString());
    	}
    }
    finally
    {
    }
    ```
    
    - The enumerator produced by `GetEnumerator()` implements the `IDisposable` interface which disposes the created object automatically once out of scope.
    - The finally block will host the code for disposing the enumerator.
        - Method will vary depending on if the operation is synchronous or asynchronous.
        - If there’s no explicit conversion available to `IDisposable` or `IAsyncDisposable`, the finally block will simply be an empty block.
- There is minimal need to remember this information as `foreach` will handle all of this for us.
    

### Technical Implementation

- When you write an iterator as a method, the compiler translates it into a state machine with nested classes.
    - This class will track the position of the iterator as long as the foreach loop in the client code continues.
- Compiler generates the other `IEnumerator` methods `Current`, `MoveNext`, and `Dispose` once it detects an iterator method.

### Use cases

- When you want to modify the list sequence after the first foreach loop iteration.
    - Modifying the list in a foreach loop without using iterator methods will cause the application to throw a `InvalidOperationException`.
- Avoid fully loading a large list before the first iteration of a foreach loop.
    - Example includes a paged fetch to load a batch of table rows.
- Building the list’s content in the iterator method.