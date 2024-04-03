# ⭐ Asynchronous Programming

### Overview

- Follows what is known as the Task based asynchronous programming pattern.
    
- To demonstrate the core principle of asynchronous programming, a program can:
    - execute all tasks, or call the methods which will return a `Task` object first
        - At this point, all the methods are being executed. The Task object will contain information if the async method is done executing, and only then the resulting value if the `Task` object expects a typed value, can be accessed.
    - down the sequence of code written, the `Task` objects’ values that are collected from executing all the asynchronous methods can then be accessed by using the `await` keyword.

- Async method calls will run according to the order that they were placed on the code. However, there’s no guarantee that the first task that was called will be the first task that will be completed.
- Placing the `await` keyword on a Task means that the code will wait for that code to finish before moving forward to the next line of code. This can be delayed by defining the Task first to a variable, and then placing the `await` keyword for when it’s value was supposed to be used.
- When a method using await itself is not awaited, execution of the method that called the async method will continue before the completion of the execution of the async method was completed.
- Any method that has an async operation, that method is automatically identified as an async method.
- Async methods cannot declare in, ref and out parameters, but it can call methods that have those parameters.
- Exceptions are thrown only when a Task is awaited.
- `Task.Run()` creates a new background thread. This is appropriate for CPU bound tasks.
    
- `Task.ContinueWith()` method continues an execution of a Task after it has been completed or reached a certain state. See example code here: [https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)
    
- Using async with LINQ is powerful but should be used very carefully. A code with LINQ and async that is not written correctly can result to blocking code, and can become unwieldy.
    

### Async streams

- `IAsyncEnumerable<T>` is useful for returning an async stream. An example would be reading through a file, returning a stream of words per line. Callers would enumerate these words using a `await foreach` statement.
- This is useful for getting large datasets asynchronously from a data source as this can optimize the code memory-wise by not allocating any list data types to store all the data, and instead allow the caller to handle the continuous stream of data that were coming from the method which is returning data using yield.

### `ValueTask`

- `ValueTask` is a value type, a struct.
- Uses less memory than `Task`
    - Since it is a value type, its value will be stored on the stack which means they get automatically cleaned up by the GC once it goes out of scope.
- In a scenario where a method only runs an async operation if a certain condition is met, and the rest of the code inside that method runs synchronously, using the `ValueTask` rather than `Task` is highly beneficial.
- In order to use `ValueTask` return type, you must add the Nuget package, `System.Threading.Tasks.Extensions`
    - It seems like there is no need to do this for .NET 7.0 as it is immediately available without needing to install said Nuget package.
- Specific use cases:
    - For methods that have synchronous hot paths. `ValueTask` should not be used for tasks that may take a long time to complete.
    - Use `ValueTask` if it was only going to be awaited once.
        - If a `ValueTask` is going to be used more than once, convert it to `Task` first using the method `AsTask`
        - The `AsTask` method cannot be called more than once.
    - If a code will not run in multiple threads.

### `async void`

- `async void` should only be used by event handlers, as events do not have return types. `async Task` should be used almost always.
- `async void` statements cannot return exceptions that were thrown inside their code block. It will cause an application to crash because of the absence of error handling capabilities.

### Miscellaneous Ideas

- `TaskCompletionSource<T>` is a class that allows some asynchronous operation to turn into a Task.
    - It is a part of some legacy code from before when async/await is not available in .NET yet.
    - For more information, check out [https://www.pluralsight.com/guides/task-taskcompletion-source-csharp](https://www.pluralsight.com/guides/task-taskcompletion-source-csharp)

- `AsyncLocal`    
    - used to persist data across threads.
    - provides optional notifications when the value associated with the current thread changes, either:
        - because its “Value” was explicitly updated, or
        - implicitly changed when the thread encountered an await.

- `ThreadLocal`    
    - does not persist data compared to `AsyncLocal`
    - loses its data in Value property, when an await code is reached.

### Useful Information

[[dotnet-core-development/csharp/misc/async-programming/how-async-await-works|How async await works?]]