# **Delegates**

---

## Introduction to Delegates

- Provides a _late binding_ mechanism in .NET.    
    - Late binding is the ability to provide method that will be invoked inside another method.
    - Similar to a callback in Javascript. However, the callback function here in C# is type-safe.
    - An example would be a sorting operation.
        - Sort does one thing and that sort is based on a specific criteria.
        - The criteria is provided in the sort method as a function that is executed inside the aforementioned sort method.

- LINQ is heavily reliant on delegates.
- Makes use of the classes `System.Delegate` and `Sytem.MulticastDelegate`.
- The method that can be used as a delegate can either be static or an instance method.
- Instead of using delegate types, a function pointer can be used as well by using the `delegate*` syntax.
- Delegates of different types cannot be compared and will result to compilation error.

### **Multicasting**

- Allows for multiple delegates to be combined together in one variable via the `+` operator.
- When the variable is used, it will call all the assigned delegates in order.
    - The order is based on what delegate was assigned first.
- Removing a delegate from the multicast can be done by using the `-` operator.

## `System.Delegate` and the delegate keyword

- Defining a delegate type is like defining a method signature with the delegate keyword on it.
- A delegate type is a generic type.

### How to use?

**Declaration**

```csharp
public delegate int Comparison<in T>(T left, T right);
```

**Instantiation**

```csharp
public Comparison<T> comparator;
```

**Invocation**

```csharp
int result = comparator(left, right);
```

- In order to pass in the created delegate to a method that accepts a delegate parameter, say `List.Sort()` for example, there are a couple of ways to do this.
    
    - For the first two approaches to be discussed, it is a prerequisite to declare a method.
        - This method can be private, but it should contain the same return type and number of method arguments.
    - The method can then be provided directly into the `List.Sort()` method without the arguments.
        - Compiler will convert the method reference into a reference that can be used as a delegate invocation target.
    - The method may also be assigned into a delegate type variable. That variable will then be supplied to the `List.Sort()` method.
    - The last approach is using a lambda expression and directly providing that lambda expression into the `List.Sort()` method.
- Every delegate defined is derived from the `MulticastDelegate` task.
    
    - The multicast delegate means that more than one method can be invoked when invoking through a delegate.

### Strongly Typed Delegates

- Declaring a delegate the way it was described on the previous section is tedious.
    - Every method that we define should have its own delegate type that will match its exact method signature, the return type and its arguments.

- .NET Core framework has types that can be reused to declare delegate types.
    - **`Action`**
        - An Action delegate can contain up to 16 arguments.
        - Used when the delegate has a void return type.
    - `Func`
        - A `Func` delegate can contain up to 16 arguments.
        - An additional type parameter is included in its definition which is reserved for the return type of the delegate.
        - Used when the delegate has a non-void return type.
    - Predicate
        - Returns a test on a single value.
        - Useful for when validating a specific collection if an item meets the criteria defined in that predicate.
        - Can also be rewritten as `Func<T, bool>`

## Common patterns for delegates

- The example used here was a logger implementation that can log messages in the console and in a file.
    - This implementation can be achieved with the inheritance pattern, with a base Logger class definition then having a console logger and a file logger inheriting from the base Logger class.
    - Delegates pattern provides an additional way of implementing these type of implementation and functionality.

- You can hook up as many method invocations on delegates as possible.
    - The underlying delegate infrastructure allows for multiple method invocations to be called when implementing a certain functionality.
    - These method invocations will never clash with one another due to the infrastructure handling delegates really well.

- With the delegate pattern, the base Logger class has minimal changes necessary.
    - It does not care what implementation is defined by the console logger and the file logger.

- On plain sight, the classes involved with handling delegates (`System.Delegate` and `System.MulticastDelegate`) does not contain an Invoke method. However, a delegate type can use this method. Why?
    - Compiler will generate a type safe `Invoke` method that the program can use to invoke a delegate.
    - Useful for handling null delegates.

## `CreateDelegate`

- Allows creation of delegates at runtime or in a dynamic environment where a delegate should be created based on user input or data encountered in the application.
    
- Most of the overload of this method features the `firstArgument` argument which refers to the first argument that will be supplied to the delegate.
    - This dictates if the delegate will be open or closed.
        - An open delegate means that the first argument is `null` and can be supplied when the delegate is invoked.
            - For instance methods, `firstArgument` is meant for the type that contains the calling method.
            - For static methods, `firstArgument` is null since an instance of that type is not needed for invoking a static method.
        - A closed delegate means that the first argument is provided and this argument will always be used when the delegate is invoked.
            - Same rules apply for instance methods on open delegates.
            - For static methods, `firstArgument` can be used to supply the first argument of the static method.

- Check out the branch `dotnet/delegates/create-delegate` to see the code demos.