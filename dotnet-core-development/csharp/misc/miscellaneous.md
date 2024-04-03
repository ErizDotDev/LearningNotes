# Miscellaneous
---

### Interlocked

- The static class `Interlocked` is used to avoid race conditions, in a multi threaded code.
    - In a scenario where we increment a value of a variable in a multi threaded code, it is impossible to come to a value of a constant variable because different threads will hog over resources to see which one will be able to increment to the value of the variable. This operation is known as very **unatomic**.
    - To do this in an atomic way, we use the static class `Interlocked` with an available method `Increment()` which allows multi threaded code to do increments safely.
    - Threads will wait for an increment operation (which is a multi step process by the way) to finish before another increment operation is executed.

Reference: [https://dotnettutorials.net/lesson/interlocked-vs-lock-in-csharp/](https://dotnettutorials.net/lesson/interlocked-vs-lock-in-csharp/)

### `CancellationToken`

- Provides a way to cancel operations between threads, thread pool, or Task objects.
- Can be used to cancel async operations prematurely with the `CancelAfter()` method.

### `IGrouping<T1, T2>`

- New data structure that acts like a dictionary, but the value is an `IEnumerable` of type T2.
- To get the value portion of the data structure, you need to convert it first into a `List` to capture the data inside it.

`lock`

- Used to synchronize shared resouces in a multi-threaded environment.
- Helps prevent multiple threads from accessing the same resource simultaneously (race conditions) and causing conflicts and inconsitencies.
- When the `lock` keyword is used, you specify a synchronization token.
    - The synchronization token can be any object, but it should be the same object used across all threads.
    - The synchronization token will act as the lock, and it will only allow one thread to execute the statements inside the lock code block.
    - Other resources will need to wait for the statements inside the lock code block to be released by the executing thread.