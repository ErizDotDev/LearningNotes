# 🚨 Events
---

[[dotnet-core-development/csharp/concepts/events|See this section for initial information]]

### Event, delegate and `EventHandler`

- The `event` keyword is required to create an event.
- Use `delegate` if the event method signatures should follow a template complete with a return type and parameter list.
- Use `EventHandler` or `EventHandler<T>` if the event method signatures should follow a template but does not require a return type or a parameter list.
    - A simpler option to use compared to using `delegate`.

---

## Observer design pattern

- Enables a subscriber to register and receive notifications from a provider.
- Useful for push-based notifications use cases.
    - Push-based notifications is a mechanism of giving out information from server to client without the client polling for the data.
        - The server will deliver the information voluntarily.

- Characterized by having a provider, or the source of the notifications, and the observers that registers to the provider and will receive notifications or data based on certain triggers: a condition that was met, an event that was raised, etc.
    - Observers are notified through a delegate that was invoked by the provider.
    - The delegate invoked may or may not contain additional information containing the details of the event.

- There is a package in .NET Core called Reactive Extensions in .NET with the entire concept of the methods in this package revolves around the `IObservable<T>` and `IObserver<T>` interfaces.

### Components of design pattern

- The provider will implement the `IObservable<T>` interface and its method `Subscribe()` which is called by the observers to receive notifications.
    - Provider should have a data structure that will hold the list of observers subscribed.
        - `List<T>` should be enough to hold all the observers.
            - The order in which the notifications are delivered to the listed observers are not defined and any method to sort the observer should be fine.
    - Provider should also have an `IDisposable` implementation.
        - This will remove observers when the notification is complete.
        - Observers will also receive a reference `IDisposable` implementation from the `Subscribe` method so they can call the `Dispose` method to unsubscribe to the provider before it can finish sending notifications.

- The observer will implement the `IObserver<T>` interface and implements the following methods that are expected to be called by the observer.
    - `OnNext` - observer will receive new data or information
    - `OnError` - observer will be notified that an error occurred
    - `OnCompleted` - observer will receive the notification that the provider is done sending notifications.

- The object that contains the data that will be sent out to the observers from the provider.
    - This type is the corresponding type parameter that will be used for the `IObservable<T>` and `IObserver<T>` interfaces.

### Best practices

- **Threading**
    - The `Subscribe` and `Dispose` methods should be thread-safe.
        - In a typical provider/observer contract, there is no clear-cut entity who should unsubscribe.
            - Because of this, either one of the observer or provider may remove the same member from the list which should be avoided.
    - A layer, or a wrapper class, should be created around a provider to let other developers know the intention of a provider if it is going to be handled on the same or a different thread.

- **Handling exceptions**    
    - Exceptions in the observer design pattern should be informational for both observer and provider.
        - This means that the exception should not disrupt the flow of the code.
            - This can be achieved through the following strategies:
                - Logging the error
                - Ignoring the error (only if the exception is not critical or cannot be easily resolved)
                - Provide a fallback or default behavior (while accepting that some features may not be available because of the encountered issue)
        - This is because of the observer design pattern’s nature of having loose coupling.

    - Providers should handle exceptions by implementing the following best practices:
        - Providers should handle its own exceptions;
        - Providers should not dictate observers how to handle their exceptions;
        - Providers should provide information about the exception to the observer, by calling the `OnError` method IF the exception affects the provider’s ability to deliver the notification.
        - Upon completing an observable by calling the `OnError` or `OnCompleted` methods, no further notifications should be delivered.
        - Both the observer and provider can successfully unsubscribe at any time, most especially the observers.
        - Unsubscribing should be a thread safe operation.

    - Observers should handle exceptions by implementing the following best practices:
        - Do not throw exceptions on `OnNext` or `OnError` methods.
            - Exceptions that are thrown regardless should not be handled.
            - This maintains the integrity and loose coupling between the provider and observer.
                - Regarding the coupling, basically, the provider should not have an idea about the implementation of its observers.
                    - If the provider knows that an observer is deliberately throwing exceptions, it will break the loose coupling principle as the provider needs to add a try catch statement to catch the exception.
                - Regarding integrity, there is an expectation that the observer will be able to handle data coming from the provider gracefully.
        - The observer needs to wrap the exception that it will throw to `OnError` to preserve the call stack.
            - A standard exception object should be enough.

    - Avoid unregistering observers in the `Subscribe` method.
        - This will cause null reference issues and unpredictable behavior especially when the observer design pattern is in a multi-threaded environment.

    - It is recommended to have a 1:1 provider to observer relationship.        
        - This will reduce performance issues caused by having multiple observers being subscribed to a single provider.
        - This also provides flexibility and scalability benefits.
