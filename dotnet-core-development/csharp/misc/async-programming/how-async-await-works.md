# How async/await works?

To understand how the whole async/await works, it is important to go back to the initial iterations of the async workflows in .NET.

## .NET Framework 1.0

- Implemented an **asynchronous programming pattern**.
- It’s a pattern that can get complicated pretty quickly as requirements get more complicated. Example: code that calls an async function inside an async function

### How it works

- Basically, a single async method will need to write additional methods as part of the pattern.
    - Start - Will start the async operation and will run a continuing code (callback) if provided.
    - End - Ensures that the operation will be completed and returns the output value, if there’s a return type stipulated.
- Both the start and end methods are relying on the `IAsyncResult` object created by the start method to facilitate the async operation as it contains the information if an operation has been completed and the result of that operation.

### Stack Dive

- Introduced the stack dive issue - where code repeatedly makes call that go deeper and deeper into the stack that could potentially result to stack overflow.
    - When the Start method is called and it completes synchronously, it can invoke a callback synchronously.
    - This means that you have the following stack frames:
        - One for calling the Start method
        - One that is for the Start method itself
        - One for the callback
    - Now, imagine a scenario where the callback function inside the Start method, calls the Start method again? This will go deep into the stack until you run out of stack.
- To resolve this, they made it so that the caller is the one to do the continuation work, if the operation completes synchronously. That reduced the stack frame required to run the operation, and it allows developers to go deeper into the stack.
    - There are two members of `IAsyncResult` that can give an insight whether an async operation is complete or not.
    - `IsCompleted` - It tells whether the operation is complete; Very straightforward.
    - `IsCompletedSynchronously` - Tells whether a task has been completed synchronously or async.
        - If a task is completed synchronously, it will return true, which means the caller will have to provide an implementation of the continuing code, that is in the callback method provided in order to proceed. This is the resolution to the stack dive issue.
        - Otherwise, it will return false, and thus the continuation code that is in the provided callback will be the one that is invoked.
    - Still not ideal! For a complex asynchronous code, this will be unwieldy to write and maintain.

## .NET Framework 2.0

- Rise of the **event based asynchronous pattern**. **(EBAP)**
    
- Using the concept of schedulers to queue executable pieces of code into a scheduler.
    
- This pattern exposes a function that will start the asynchronous operation (let’s call it the async method) and an event that will be raised once the async operation is completed.
    

### `SynchronizationContext`

- Works as an abstraction for a general scheduler.
- It has a method called `Post` that allows for a work item to be queued in whatever scheduler the `SynchronizationContext` is representing.
- It introduced a concept of scheduling that works in different application models.
- For example, to schedule a task for Windows Forms, they need to use the method `BeginInvoke`. For WPF, it’s `Dispatcher.InvokeAsync`.
    - `SynchronizationContext` sort of provides a common interface in order for the operations to be recognized in a common language.

### EBAP and `SynchronizationContext`

- EBAP states that an operation’s completion should be queued to whatever `SynchronizationContext` was current when the async operation was initiated.
- Components
    - `AsyncOperation` - Tuple that stores the object supplied by the user, and the captured `SynchronizationContext`.
    - `AsyncOperationManager` - A factory that does the capturing of context, and initializes a new instance of `AsyncOperation`
    - `OperationStarted` and `OperationCompleted`
        - Enables recognition of in-flight operations.
        - These methods are called at the start and end of each operation, respectively, so that it could inform the `SynchronizationContext`, and so that the `SychronizationContext` can track the effort.
        - Only issue here is that there is no work to be tracked individually as most of the methods that start these async operations, have `void` return types.

## **Tasks**

- Task is a data structure created to represent an operation and when that operation completes, the Task object will hold that value.
- One feature that makes it remarkable is the fact that a Task can handle its own synchronization and ensure continuation, regardless of whether a task is completed or not, or if it is completing concurrently with the notification request, while being able to notify asynchronously that a Task is already completed.
- Task resolves some of the primary issues revealed with the previous iterations of .NET’s asynchronous programming pattern such that:
    - You don’t need to provide the continuation code, or a callback, to start the async operation.
    - Everyone who has async operations can return a Task; and
    - Everyone who consumes an async operation can consume a Task and nothing needs to be done to connect the two.

### A simple implementation

- How it works is a combination between the ideas of its predecessors.
    - The following example is a simplified implementation of the Task class.
    - There are a couple of members that live inside Task
        - A variable that is used to determine whether the operation is already complete.
        - A variable that stores the error encountered inside the operation.
        - Continuation code, a callback (In the example, it only showed a single Action object. But in reality, it is a `List<>` of Action objects.
        - A `ContinueWith()` method; It allows Task to have the ability to supply the callback after the operation was initiated. Inside, the code will look like this:
            - If the task is already marked as completed by the time `ContinueWith()` was called, it will queue up the callback for execution.
            - Otherwise, it will store the callback such that that callback will be queued after the operation is completed.
        - A method that will set the result;
        - A method that will set the error; (In reality, this will support multiple errors or exceptions thrown)
        - These two methods, in reality, do not exist for Task, as Task is only made to be consumed. But there is a class that acts as a producer on behalf of Task. `TaskCompletionSource` have the methods that will set the result and the exceptions encountered on a Task.
        - A method that will be completed once the operation is complete. It will also invoke the previously stored continuation code.
        - Another method that will store the exception thrown during the execution of this async task, or return a result. This method is also allowed to block allowing for the operation to complete, which is implemented with the `ContinueWith()` method.

### `ValueTasks`

- Used to avoid performance overheads.
    - An example of this would be the Socket method that has a `SendAsync` and a `ReceiveAsync`. Every time a socket will send or receive anything they will be allocating a Task object even though both send and receive functions can do their operations in a synchronous fashion.
- Task needs to be allocated because it’s an object. But results of Task and `Task<TResult>` can be cached if the value is commonly used.
- There are data types that can have trivial cache allocations, like bool, small Int32 values, and the default value of various return types.
- Should be used with a generic type, `ValueTask<TResult`. A simple`ValueTask` return can be achieved with `Task.CompletedTask.`

`**IValueTaskSource<TResult>**`

- Allows an implementation to have supplemental functionality to its `ValueTask<TResult>` by exposing important methods like providing the continuation code to the async operation and getting the result of that async operation.
- Allows for bi-directional transition from complete and incomplete state. Whereas, with `Task<TResult>` once it is completed, it can never go back into being an incomplete state.

## Iterators and Callbacks

- At some point, the continuing code are still callback functions. And developers are still forced to do a continuation-passing style to do async control flows.
- Iterators helped in resolving this issue.
    - Involves the use of the interfaces, `IEnumerable<T>` and `IEnumerator<T>`
- Iterators can create a full-blown state machine.
- A Task can be represented as a single representation of any async operations. But these async operations can be treated as a list of operations that are needed to be handled. Since we are talking about a list here, this can get enumerated and iterated.
- The concept of iterators is tied with the `yield return` keywords. Using the iterator approach can establish a control flow in the async code and handling it can be straightforward. The previous iterations of async programming meanwhile cannot handle complex control flows and convert it into async code.

## How it works?

### Compiler Transform

- The `async` keyword is removed, as it is only a part of the implementation detail, or really just surface level detail.
- Compiler also created a struct - a state machine for the async method.
    - Logical equivalent of the use of `IEnumerable<T>` and `IEnumerator<T>` in the iterators.
- If the async method completes synchronously, this state machine will never leave the stack. In other words, there’s no allocation that happened on the state machine unless the operation is completed asynchronously.
- C# allows for “task-like” types to be returned from async methods, as long as a builder type is created to associate with the task-like type. The two can be associated using the `AsyncMethodBuilder` attribute.
    - In this context, the builder is something that can create an instance of that type, complete it either successfully, return a result if specified, handle exceptions, and handle continuing code.
- The generated state machine is initialized to have a state of - 1, as the state will logically start from the beginning at 0. The state is moved via the `MoveNext` method concept found in iterators.
- A function will be called to start the iteration. But the content of that function is essentially just the `MoveNext` method. Why isn’t it called directly? To answer this, we must understand first `ExecutionContext`.

### `ExecutionContext`

- Is a type of transport device that allows ambient data to flow from one async operation to another.
- To understand the role of `ExecutionContext`, we need to understand ambient data.

**Ambient Data**

- Ambient data is what you call the data that are not passed to functions via parameters to update state. Instead, they just sort of sit there and available to be consumed, if needed. Most often, they’re static variables.
- We can take the concept of ambient data a step further and refer to thread-local state.
    - A thread-local state, in .NET, are static fields attributed with `[ThreadStatic]` or has the data type `ThreadLocal<T>`. Its difference to the earlier example being that its data is only limited to the current thread of execution, with each thread having its own isolated copy of those static fields.
    - Given the above characteristics, we can have a fully isolated form of implicit passed data by:
        - populating the thread static
        - make the method call, and
        - upon the completion of the method, revert the changes to the thread static.
- In an asynchronous context, what if a call is made to an asynchronous method and the logic inside that asynchronous method wants to access that ambient data? How can that be done?
    - If the data were stored in a regular static, it can be accessed by the async method. But multiple callers can overwrite each other’s state when trying to write to these shared static fields.
    - The async method can still access the data if it were stored in a thread static, but only up until the point where it stopped running synchronously on the calling thread.
        - If it hooked up a continuation to some operation it initiated, and that continuation ends up running on another thread, it no longer has access to the thread static information.
        - Even if it runs by same thread, either by chance or the scheduler forced it to, the data would’ve likely been removed or overwritten by some other operation initiated by that thread.
    - A mechanism is needed for ambient data to flow through async points, such that throughout an async method’s logic, wherever and whenever that logic might run, it would have access to that same data. And this is what `ExecutionContext` is.

**How `ExecutionContext` works?**

- `ExecutionContext` lives in a static thread. But when an async operation is initiated, it:
    - reads a copy from that thread static,
    - stored,
    - and then when the continuing code of that async operation is run, the `ExecutionContext` will then be restored to live in the `[ThreadStatic]`on the thread which is about to run the operation.
- This is the mechanism used to implement `AsyncLocal<T>`
    - If you store a value in `AsyncLocal<T>` and then queue a work item to run in a thread pool, that value will be visible in that `AsyncLocal<T>` inside of that work item running on that pool.

### Continuation of the discussion on Compiler Transform…

- The function that is called to start the iteration will also need to factor the `ExecutionContext`. Inside this method, after moving the iterator to the next element, it will restore the `ExecutionContext` to what it was before the `MoveNext` method was called.
    - This is to prevent ambient data leakage from an async method to its caller.
        - For example, we called an async method that impersonates another user and their permissions. This operation, more often than not, flows in asynchronous operations. In this example as well, this async method calls another async operation.
        - If we do not restore the `ExecutionContext` to its previous value, and the above async method was called with the async call to another async method not yet completed, the control will flow back to the caller function, with the value of `ExecutionContext` intact on the current thread.

**`MoveNext`**

- One of the functions generated by the compiler for asynchronous operations.
    
- This method is called after starting the iteration.
    
- The responsibility of this method is to execute the code inside the original async method, complete its execution, and handle any exceptions thrown or return a result. Doing the last two actions will complete the Task.
    
- It is setting the state of the state machine to indicate completion.
    
- Remember the builder struct code that the compiler also generated from earlier? It has another task.
    
    - Suspension handling - When an async operation is suspended, it is the builder’s job to generate a Task that will be returned as a result.
    - If the method is never suspended, and the builder doesn’t yet have a task, the builder can create a completed task.
- The task has three possible final states: success, failure and canceled.
    
- The builder can do translations it deems necessary for the type of object it’s creating, in this case a Task object.
    
    - If the async method encounters an exception that is derived from `OperationCanceledException`, the task‘s final state will be `TaskStatus.Canceled`; Otherwise it will be `TaskStatus.Faulted`
    - The external implementation does not care about this detail, except for the code that interacts with Task such as `ContinueWith`.
- The code generated by the compiler is not as convoluted as that of the APM’s (the .NET Framework v1.0 async programming solution) implementation. It is still wordy but the compiler does all the heavy work of rewriting the code.
    
    - Compiler rewrites the async method code in the continuation passing pattern while ensuring that state is preserved for this continuation.

**Awaiter Pattern**

- In typical C# fashion, they provided APIs to enable awaiting for objects that are not the Task object.
    
- The awaiter pattern follows a similar principle to the foreach code that follows the **enumerator pattern**.
    
- A type can be awaited if it exposes a `GetAwaiter()` which a Task has.
    
    - The `GetAwaiter()` method needs to return something that will expose several members such as the `IsCompleted` property, which is used to check if an operation is already completed.
    - If the value of `IsCompleted` property is true, it usually will check for another member of the awaiter class which is the `GetResult` method.
        - `GetResult` will return an exception if an operation failed in order to propagate it out of the await in the async method;
        - It will also return the result of the operation, if there is one.
    - However, if the value of `IsCompleted` is false, it will suspend the execution of the async method until the awaited operation completes.
        - This means returning out of `MoveNext` - In the external implementation, this means returning the Task out to the caller.
        - Before the above step happens, a continuing code will need to be hooked up to the Task being awaited
    - The continuing code is hooked up by the awaiter by implementing the following interfaces:
        - `INotifyCompletion` which has the method `void OnCompleted(Action continuation)`.
        - `ICriticalNotifyCompletion`, which is inherited from `INotifyCompletion`, that has a method `void UnsafeOnCompleted(Action continuation)`.
        - The difference between the two is that the `ExecutionContext` will flow on the `OnCompleted` method. It won’t on the `UnsafeOnCompleted` method.
        - Historically, the existence of these two methods were due to the Code Access Security (or CAS) which does not exist anymore in .NET Core. CAS is primarily used for its legacy partial trust feature.
            - When partial trust is enabled, CAS information will flow as part of the `ExecutionContext`, but flowing this feature is unsafe.
- At this point, we have to remember that the state machine struct is in the stack.
    
- We need to preserve the state machine so that when it resumes, we have all the correct state. This means, that the state machine should be stored somewhere on the heap.
    
    - The stack will be used by other upcoming, unrelated work performed by the same thread.
    - The continuing code needs to call the `MoveNext` button on the heap.
- The `ExecutionContext` should also be incorporated on the continuing code.
    
    - The state machine needs to ensure that any ambient data stored on the `ExecutionContext` is captured at the point of suspension and then applied at the point pf resumption.
- A helper method is needed to do the following operations:
    
    - Code is needed to ensure we only transfer the struct from the stack to the heap the first time the method suspends execution. We used the heap object where the struct was transferred into as the target of `MoveNext`
        - It is important on these series of steps to ensure that we’ve captured the correct context and upon resumption, ensure that we are using the captured context to invoke the operation.
    - There is a lot of complicated above and we cannot let the compiler to emit all of it, so it should be enclosed in a helper method.
    - The logic above should also be customizable as part of implementing the builder pattern.
    - We’d want evolve the logic above and improve that logic.
    - The mentioned logic was overhauled on .NET Core 2.1.
        - `ExecutionContext` became an **immutable** data structure.
            - The downside of this change was if there are changes in the context, a new `ExecutionContext` is initialized.
            - The upside, however, is copying the `ExecutionContext` is not needed to flow data.
            - Capturing the context is now as simple as reading it of a field rather than reading it, and making a copy of its contents.
        - `LogicalCallContext` does not exist anymore, which means less data that will flow with the `ExecutionContext`.
            - Now, every information that previously flows with `ExecutionContext` now has their own separate contexts. `ExecutionContext` now just serves as the storage for `AsyncLocal<T>`.
        - `QueueSegment` / `IThreadPoolWorkItem[]` - The current implementation now of `ThreadPool` is it is now a `ConcurrentQueue<T>` which has been rewritten to become a linked list of circular segments of **non-fixed size**. This list becomes large enough to handle list enqueues and dequeues which means no additional segments are needed to be allocated.
    - For comparison here’s the differences between the implementation on .NET framework
        - `ExecutionContext` is a **mutable** data structure. Before, a copy was made on the `ExecutionContext` so it will not see the mutations performed for every fork of an async operation.
        - `LogicalCallContext` - The implementation detail of `AsyncLocal<T>` on .NET framework. To put it simply, it is the general state that flows with the `ExecutionContext`
            - This means that the number of copies that is done with `ExecutionContext` is also the number of copies that `LogicalCallContext` has.
        - `QueueSegment` / `IThreadPoolWorkItem[]` - Its implementation during this time is that it is a linked list of circular segments that are not reusable. It will be collected by GC once it has been enqueued and dequeued.

`**AwaitUnsafeOnCompleted()**`

- Has a vast difference between its implementation in .NET Framework and .NET Core.
- This section will cover the implementation details in .NET Core.
    - Use `ExecutionContext.Capture` to grab the current context.
    - Compared to the .NET Framework implementation, the `AsyncTaskMethodBuilder`, or we can simply refer to it as builder, will only have a single member which contains an instance of `AsyncStateMachineBox<TStateMachine>` where `TStateMachine` is the type of the compiler-generated state machine struct.
        - `AsyncStateMachineBox` is the magic.
        - Instead of boxing the state machine, the struct will live as a strongly typed field on the task.
        - The `ExecutionContext` and the action are both stored in here, and they both exists as fields.
        - We have direct access to the instance since it is stored on the singular field in the `AsyncMethodTaskBuilder` which means we don’t re-allocate things on every suspension.
        - If `ExecutionContext` needs to change, we just overwrite the value of the field, and we don’t need to reallocate anything. We only need to allocate if the method is suspended for the first time.
            - The allocation will follow the set of logic discussed earlier where the state machine will be copied from stack to the heap while ensuring that the state machine will appropriately and recursively reference itself.
    - We can get an Action to a method on this instance that will invoke its `MoveNext` method that will do the appropriate `ExecutionContext` restoration before calling the state machine’s `MoveNext`.
        - The Action that was recently called will be cached so that it can be easily reused when called again.
        - The Action will also be passed to the awaiter’s `UnsafeOnCompleted` to hook up the continuation code.

## `SynchronizationContext` and `ConfigureAwait`

- `SynchronizationContext` makes it possible to call reusable helpers and automatically be scheduled back whenever and wherever environment it deems fit.
- It is the awaiter implementation’s responsibility for invoking and queueing the continuing code when the async operation is completed. So, the awaiter needs to integrate with `SynchronizationContext`.
    - Task, `Task<TResult`, `ValueTask`, and `ValueTask<TResult>` respects `SynchronizationContext.Current`. Custom awaiters do not.
    - This means the four default awaiters look up the current `SynchronizationContext` and then it is successfully get a non-default one, will eventually queue up the continuation to that context.

**Technical Implementation**

- The logic discussed on the last main bullet point was stored in a code of a method called `UnsafeSetContinuationForAwait`.
- This method will wrap the `IAsyncStateMachineBox` (You can remember this from the `AwaitUnsafeOnCompleted` method above) to also contain the scheduler, if is currently existing.
- If a non-default `SynchronizationContext` exists, it creates a `SynchronizationContetAwaitTaskContinuation` object where the continuing code will be stored). This also will wrap the current captured `SynchronizationCOntext` and knows how to invoke the `MoveNext` in a queued work item.
- If it could not find a custom `SynchronizationContext` to use, it also looks to see whether the `TaskScheduler` type that is used by Tasks has a custom one that it can use. If there is, just like with `SynchronizationContext`, it wraps the scheduler and continuation in `TaskSchedulerAwaitTaskContinuation` object.
- At the start of the method, it checks if the code will continue on captured context.
    - These checks are done for `SynchronizationContext` or `TaskScheduler` if the value of the flag is true.
    - If false, it will treat the schedulers provided as default values, and ignores them.
    - `ConfigureAwait` will set the value of this flag’s value.
- The only role of `ConfigureAwait` on an await is to feed its boolean value to the function, to skip the checks on the last bullet and behave as it neither of them existed.
- In the case of Task, it will allow Task to invoke its continuations wherever it sees fit rather than force it to queue them to execute them on some specific scheduler.

`**OperationStarted`, `OperationCompleted`, and `async void`**

- We use async void for handling async operations inside event handlers.
- Event handlers are defined as a void returning delegate. In order to provide a method that matches this delegate, the method also needs to return void.
- An async lambda is an async void method.
- A method that accepts an async lambda method will only invoke the lambda method. It has no idea that the async method is still “running” and will asynchronously complete later.
- Async void methods are similar in nature to the EAP methods discussed earlier.
- `AsyncVoidMethodBuilder` the builder associated with creating async void methods generated on the compiler, will place `OperationStarted` and `OperationCompleted` inside the generated method to know when an operation is started and completed.

### State Machine Fields

- **State** field
    - Defines the current state of the state machine.
    - Possible values:
        - -2 - operation has been completed
        - -1 - `MoveNext` is about to be called for the first time, or `MoveNext` is currently running on some thread.
        - > 0 - method is suspended. The value of the state indicates which await is suspended.
            
            - If multiple async operations were identified, the code will follow the top to bottom approach.
- **Builder** field
    - Builder of the state machine.
    - Responsible for the lifecycle of the async method, such as:
        - creating the return task
        - eventually completing the task
        - serving as an intermediary for suspension
- `source` and `destination` fields
    - These are method parameters.
    - All method parameters that are used by the method body need to be stored in the state machine so that `MoveNext` can access them.
        - Compiler can optimize such that if it sees an unused parameter, it will be omitted on the generated code.
- The **buffer local** field
    - The buffer local that got translated into a field so that it can survive across await points.
    - Compiler can also omit this field if it doesn’t see the need for the value to be preserved across await points.
- All the await operations found in the async method
    - The compiler needs to get the output awaiters’ (generated by each await operations) value prior (`IsCompleted`, `UnsafeOnCompleted`) to the await and then needs to access them (`GetResult`) after the await.
        - Highlighted fields or methods are fields that the compiler needs to access at certain states, as specified.
    - Compiler will reuse these fields whenever possible.
    - For multiple await operations, the compiler will generate one of each type of awaiter return types.
- Wrap fields
    - Depending on the operation that was executed before an async operation, the compiler will need to preserve the value of the previous operation before executing the async operation so that it can be processed together.
    - These are the fields that you would want to check first if you’re hyper optimizing code.

**Reference:**

[https://devblogs.microsoft.com/dotnet/how-async-await-really-works/](https://devblogs.microsoft.com/dotnet/how-async-await-really-works/)