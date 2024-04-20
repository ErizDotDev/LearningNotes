# ⭐ Exceptions
---

- The exception model in .NET features the following benefits:
    - Throwing and handling an exception works the same for all .NET programming languages.
    - Allows each language to have their own syntax for processing exceptions.
    - Exceptions can be thrown across processes and machine boundaries.
        - Example: Client-server communication using APIs created in .NET
            - Exceptions that are caught in the server code can be returned to the client as part of the response.
    - Exception handling code can be used to increase an application’s reliability.

- When creating user-defined exceptions, always ensure that the three constructors are being applied to the custom exception.
    - One is a parameterless constructor;
    - One is a constructor that accepts a `string` message parameter; and
    - A constructor that accepts both a `string` message parameter and an `Exception` inner exception parameter.

- Use a satellite assembly to introduce localized exception message.    
    - A satellite assembly is a .dll file that basically holds the resource files for a specific language or culture.
    - Utilize the properties created in the resource file for each exception message that will be written.
        - Resources will be inspected and the current machine culture should find the respective resource file that matches it and uses that.

---

## Exception class

- Proper way of using `InnerException` property
    - Use `InnerException` to properly describe the issue that was encountered.
    - Most of the time, we throw an exception that can be easily identified by developers but is not easily understandable for someone who is not as adept in reading code.
    - It is best that when an exception is caught and the exception is not straightforward, to wrap this exception into another exception that can help easily identify what the issue is and to attach the original exception to give more detail on the encountered issue.

    - Example: A `Main` method called a method that accepts an object value, and inside this method, the object is then unboxed to become an integer value.
        - Now the `Main` method made the mistake of passing in a string value boxed into an object value to said method, and when the method tries to parse the object value into an integer, a `FormatException` was thrown.
        - `FormatException` is a known exception but it does not accurately describe what happened in the method where the caller method has passed an argument value that should not be accepted by the method.
            - It would be helpful to use an `ArgumentException` to give an accurate description of the issue to the developer and then attach the original `FormatException` to this new exception.

- Only throw objects that inherited from the `Exception` class.    
    - Though it is possible to throw an object type in a catch block.
        - There is minimal support for code that uses an object type instead of an `Exception` type.

### Data Property

- This property holds additional exception metadata that can give developers further information about the exception that was thrown.
- Information is held as a list of key value pairs.

- Ensure that the application can still be executed correctly because this Data property can get corrupted.
    - It can get corrupted when exceptions thrown by lower-level function get caught by higher-level functions.
        - The higher level functions can then modify the Data property thus removing the integrity of the values inside said property.
        - One of the things that can happen in this operation is key conflict where a value for an existing key is being modified.
            - This operation will throw an exception.

- Key conflicts can be avoided by using the namespace naming template for properties under a specific class and namespace.
- Using this unique, somewhat pre-ordered set of keys can help the program consolidate the exceptions from the lowest to the highest level exception handler and do appropriate processing on it.
- Make sure that it is used sparingly as adding data to this property can cause an exception object to become bloated and less efficient.

---

## Best practices

- It is good practice to have a catch block for a specific exception that is expected to be thrown by the code.
    
- The order of catch blocks is important.
    - The catch blocks should be sorted from the most specific exception (`ArgumentNullException`) to the most generic exception (`Exception`)

- Add more information to an exception that is being re-thrown which can provide more information when debugging.    
- When current code does not have exception handling logic, you can let the methods further up the call stack (the caller methods) to catch that exception.
    
- Clean up resources that are allocated with `using` or `finally` blocks.
    - Use `using` to automatically clean up resources when exceptions are thrown.
    - Clean up in `finally` blocks for resources that don’t implement the `IDisposabble` interface.
        - Code in `finally` blocks almost always runs after an exception is thrown.

- Only use try catch when a failure event does not occur often.    
    - Besides, the try catch statements do not impose any performance overhead to the code especially when no exceptions are caught.

- If an issue is being caught often, use other error handling techniques.    
    - Some of the common error handling techniques is checking if an object (a database or a file connection) is null or is in a state where doing further actions on it might cause exceptions to be thrown.
        - Validate the state of an object first before doing further actions from it.

- Design classes to avoid throwing nulls.    
    - Create methods or properties that can help the client code check possibilities that can prevent it from throwing an exception.
    - ~~Methods to return a default value instead of throwing an exception.~~
        - ~~For value types, consider all options whether to use a nullable type.~~
            - ~~While it has its benefits like declaring an intention of having a specific value to be returned or not, it has its cons too.~~
                - ~~Cons include introducing more complexity as additional cases are needed to be checked and these cases might become potential sources of issues.~~

- Throw exceptions rather than error codes.    
    - This makes sure that failure is noticeable.

- Make sure exception messages are gramatically correct.    
- Introduce localized exception messages for every exception your application throws, when needed.

- Provide additional information to exceptions when needed.
    - Example: A `FileNotFoundException` has a `FileName` property.

- Place messages on throw statements as this will make a stack more informative.

- Make use of helper methods that return an exception, when needed.
    - Personal Take: Only use this method when building complex user-defined exceptions with many properties.
    - A simple throw statement followed by the exception type and message is enough most of the time.

- Ensure to rollback your transactions or revert changes to application state when an exception is encountered.    
    - Given a scenario where you have a cash withdrawal code and the withdraw code fails and throws an exception, it does not make sense to leave the withdrawn amount deducted from the account balance, right?
        - Rollback the transaction so that the deduction from the active balance did not happen as the actual withdrawal process failed.

- When creating user-defined exceptions, ensure that the exception metadata is available to the code that is executing a remote code.    
    - Given this scenario: App domain A creates app domain B and a code within app domain B causes an exception.
        - In order for app domain A to properly handle the exception, it needs to find the assembly that contains the exception data that app domain B thew.
        - If app domain B threw an exception that is contained within its code base and is not available in app domain A’s codebase, app domain A won’t be able to find the exception.
            - Because of this, CLR will throw a `FileNotFoundException`
        - To avoid this scenario:
            - Put the exception in a shared assembly accessed by both app domains.
            - Place the assembly in the GAC, if the assembly can’t be shared by both app domains.

- It is possible to capture exceptions now, exit the catch block, do some processing in the middle then rethrow the caught exception later.
    - Use `System.Runtime.ExceptionServices.ExceptionDispatchInfo` class.

- To preserve the stack trace of the current exception being caught, it is best to simply just throw the exception with a simple `throw` statement.    
    - If we use the `throw` statement with the exception, it will remove the current stack trace, and the previous stack will be seen as the first line of the stack trace.

- Practice using standard .NET exceptions instead of custom-defined ones.
    - Make it a habit to throw the most derived exception instead of the basic ones like `NullReferenceException`, `ArgumentException`

---

## Handling COM Interop exceptions

- Managed and unmanaged code can work together to handle exceptions.
- If an exception is encountered in managed code, the CLR will pass an HRESULT to the COM object that handles the unmanaged code.

- If an issue is encountered in unmanaged code which returns an HRESULT, runtime will throw an exception that can be caught by managed code.
    - The runtime can map the HRESULT from COM interop to specific exceptions.
        - Example: E_ACCESSDENIED becomes `UnauthorizedAccessException` , E_OUTOFMEMORY becomes `OutOfMemoryException`
    - If the runtime does not recognize the HRESULT, runtime will return a generic `COMException` where the `ErrorCode` property contains the HRESULT value.
    - Only COM objects that support `IErrorInfo` can have HRESULT results mapped as exceptions with information properly assigned to correct exception properties.
    - If the COM object does not support `IErrorInfo`, the `COMException` that is being returned will only contain default details.

---

## `AccessViolationException`

- This exception is thrown when unmanaged code is trying to read or write on memory it has no access to.
    - More often than not, it is because of a bad pointer value.
    - Not all read or writes with bad pointers lead to access violation.
        - When the read or writes with bad pointers are continuously not coming through, that means the memory may be corrupted, and the exception is thrown.

- This exception will only happen in managed code when it interacts with unmanaged code.    
    - In managed code, its equivalent oftentimes is the `NullReferenceException` since it is accessing null references, or references that do not exist.

- To avoid this exception:
    - Ensure that memory being accessed is allocated.
        - Exception is thrown because it accesses memory that is not allocated or not owned by any processes.
    - Ensure that memory being accessed is not corrupted.

- This exception will not be handled by .NET runtime if the exception occurs outside of the memory reserved by the runtime.
- To resolve this, just add the attribute `HandleProcessCorruptedStateExceptionsAttribute` to the method that encountered this exception.

---

## `InvalidCastException`

- .NET supports automatic conversion <u>from derived type to base type then back to derived type</u> as well as from <u>interface types to interface objects then back</u>.
- [[base-types#Type Conversion|See other conversion types here.]]
- `InvalidCastException` is thrown when the conversion of an instance of one type to another type is not supported.
	- Mostly caused by developer error.
	- Should not be handled in a `try/catch` block.

- Different from `OverflowException` where the conversion is supported but the value of the source type, a `long` type, is outside the range of the target type, a `byte` type.

- The following conversion methods will throw an `InvalidCastException`.
	- When the `Convert.ChangeType` method is called but one or both of the types involved in the conversion does not implement the [[base-types#`IConvertible` interface|IConvertible]] interface.
	- Unsupported [[base-types#^6a8a39|explicit conversions]].
	- [[fundamentals#^332b21|Downcasting]]
	- Attempting to convert an interface object (`IFormatProvider`) to a type (`DateTimeFormatInfo`) that implements the interface but the target type is not the same type or a base class type of the source type (`CultureInfo`) from which the interface object was derived.
	- Converting a type to `string` using casting.
		- Use `ToString()` instead.

---

## `InvalidOperationException`

- This exception is thrown when there is a failure to call a method for reasons other than invalid arguments.
- Usually, the exception is thrown because the state of an object does not support a method call.
- The following are common causes why this exception is thrown.

**Updating a UI thread from a non-UI thread**
- .NET GUI application frameworks only allows access to GUI objects on the main and UI threads.
- A simple example of this is calling an asynchronous function in an element event method call such as a button click.
	- Running an asynchronous task will open up a new thread that is not a main thread and if a UI object is accessed in this newly opened thread, which is also not a UI thread, the exception will be thrown.

- Resolving this will require using a framework's property that can check if the UI element operation is being executed in the UI thread.
	- For WPF, the method to call is `Dispatcher.CheckAccess()`.
	- For Windows Forms, it's `Control.InvokeRequired()`.

- If the current thread is not the UI thread, then a call to a framework's specific method will need to be made in order to direct the operation back to the main thread/UI thread.
	- For WPF, it's `Dispatcher.Invoke()`.
	- For Windows Forms, it's `Control.Invoke()`.

**Contents of a collection are being modified after an enumerator has been created**
- If collection elements must be altered while iterating through the collection, use a regular for loop.
- If the previous point does not apply, then use a temporary collection that will hold the values of the modified elements.

**Trying to sort a collection whose underlying type is not configured to be compared**
- The `Sort` methods from generic and non-generic collection types require that at least one of the objects that will be sorted implements a `IComparable` or `IComparable<T>` interface.
	- Not doing so will throw the exception.

- To resolve this:
	- Implement the `IComparable` interfaces if the source code is available for editing.

	- If the source code can't be modified, implement an overload of `Sort` by implementing a class that implements the `IComparer` interface.
		- The method inside this class will be passed as an argument to the `Sort` method.

	- Create a delegate that matches the `Comparison<T>` delegate signature which will be passed in the `Sort` method.
		- This is an alternative to the second point.

**Casting a `Nullable<T>` with a null value to its underlying type `T`**
- Example: When you define a nullable int collection with one null value, and then enumerating through each element using a `Select` statement and then casting it as a normal integer value.
- To prevent this exception:
	- Use the properties and methods available in a nullable type.
		- `HasValue` to check if a nullable type contains a value before replacing it with a different value that is not null.
		- `GetValueOrDefault()` to provide a default value to an element with a null value.

**Calling a LINQ [[standard-query-operators#Aggregation Options|aggregating methods]] on an empty collection**
- When assuming a collection is not empty, handling the exception in a try/catch block is fine.
- Use any overloads of the `Enumerable.Any` to check if a collection/sequence has any elements before doing any operations.
- Use the methods with default value fetching when available.
	- Example: `First` -> `FirstOrDefault`, `Single` -> `SingleOrDefault`

**Calling `Enumerable.Single` or `Enumerable.SingleOrDefault` on a sequence with more than one element.**
- Use the method that returns the default value when a collection contains no elements.
- Consider using the `First` or `Where` LINQ methods.

**Accessing a field from a remote domain**

---

## `NotImplementedException`

- Only use it on methods and accessors when they are still in development.
- Removing the property or method call is the only thing that can resolve this exception.
- You may also choose to throw these exceptions related to `NotImplementedException`
	- `PlatformNotSupportedException`
	- `NotSupportedException` ^572787
		- An example of where this exception is useful is when implementing a `DateTime.Convert()` to int which is not programmatically possible but the Convert method should exist as it needs to implement the `IConvertible` interface.

---

## `NotSupportedException`

- [[dotnet-core-development/dotnet/fundamental-components/exceptions#^572787|Initial information]]
- This exception indicates that a method has no implementation and shouldn't be called.

- Throw this exception when:
	- Implementing an interface and an implementation of a method does not make sense.
		- Example: Creating a date and time type that implements the `IConvertible` interface and discovering that most of the conversion operations are invalid.

	- Inheriting from an abstract base class and requiring you to override some methods but can only do so for a couple of said methods, not all.

	- Defining a type where its internal processing can be dictated by the type's state and the scenarios it leads to is impossible in the most common of senses.
		- Example: The type can only be either read-only or read-write, and if the state of the type is read-only and an operation requires an assignment of a value to this type that is in read-only state.

- Only handle this exception when:
	- Implementation does not and will not exist;
		- To handle this case, just remove the member call.

	- Invoking a member (method or property) is inconsistent with an object's purpose of state.
		- If the state of an object is known in advance, one of the ways to handle it is to make sure that the object used to interact with the object's state will support the object in whatever state it is in.
		- If the state of an object is unknown by the time it interacts with an object, make sure that the object has a property or method that gives information of whether a particular object state is allowed for a series of operations.

---

## `TypeInitializationException`

- This exception is thrown as a result of application's failure to continue processing that is maybe due to changes in configuration files, dependencies or any alterations to the application's environment.
- This exception contains additional information that may contain other exceptions that may have been thrown and their details can be found on the `InnerException` property.

- The following scenarios can trigger this exception being thrown:
	- When setting up a regex match time out value, if the value specified for the timeout does not meet the requirements, then it will thrown an `ArgumentOutOfRangeException` which is wrapped inside this exception.
		- The criteria for setting up a regex match timeout value are that it should be a `TimeSpan` object which contains a value greater than 0 and less than 24 days.
	- When a calendar is being initialized but the runtime could not instantiate the `CultureInfo` object.

### Static constructors

- The exception is usually thrown when a static constructor is unable to instantiate type because:
	- There is unhandled exception in the static constructor.
		- This is hard to debug because a static constructor is mostly not provided.
			- A compiler creates the static constructor if it is not explicitly defined.
	- Missing assembly or data file.