# ⭐ Events

---

## Introduction to Events

- Events are a way for an object to broadcast (to all interested system components) that something has happened.

- Similar to delegates in the following aspects:
    - Ease of definition; Use the keyword `event` instead of `delegate` to define an event.
        - The type that is defined with an event should be a delegate type.
    - Can subscribe to an event using the `+=` operator
    - Can unsubscribe to an event using the `-=` operator
    - Use the `?.` operator so that an event will not be invoked or executed if there are no objects subscribed under said event.

- Some rules when defining an event:
    - Event should be of void return type.
    - Event declarations should be a verb or a verb phrase.
        - Use past tense when denoting an event that has already happened.
        - Use present tense (eg. closing) when something is about to happen.
        - Oftentimes, the present tense of a verb is used to denote that behavior customization is required.

- The handler method that is used to define an event should have the “On” prefix.
- Important: Declare a local variable when passing in a value used to define an event.
    - When unsubscribing and a lambda expression was used to unsubscribe from an event, the event will not recognize something that has been registered under it, in this case a lambda expression.

- Derived classes CANNOT directly invoke events declared within the base class.
- DO NOT declare virtual events in base class and override them on derived classes. ^f3721d
- An interface CAN declare an event.
    - Explicit interface implementation requires the event declared MUST have the add and remove accessors.

- The following branches in the `DC.Lab` solution are created to demo event code in C#:
    - `csharp/events/publish-events`
    - `csharp/events/inheritance-pattern`

## Standard .NET event patterns

### Event delegate signatures

- Standard signature for a .NET event delegate

```csharp
void EventRaised(object sender, EventArgs args);
```

- Events are based on delegates and supports multiple subscribers to a single event source - multicast delegate.
- If an event is defined in a class, make it as a public field.
- First argument is of object type, and it is best practice to keep it that way, even though the developer has a good idea of what the sender object contains.
- It is not a requirement to have an `EventArgs` for your event.
    - Use `EventArgs.Empty` to declare that no event arguments will be used for your event.

- When declaring custom event arguments (arguments that are based on the `EventArgs` class), the data structure should still be contained in a class, no matter how small the data structure is.
    - The custom `EventArgs` type should be a reference type, especially when dealing with cancellation functionality.
    - The argument properties should be immutable.
        - This is so that a subscriber cannot change the values before another subscriber sees them. (This comes with some exceptions)

### Return values from event subscribers

- Events have void return types, but there are certain scenarios where certain intentions need to be communicated, such as cancelling an event.
    - We can use the `EventArgs` to achieve this.

- There are two patterns that can be used
    - One pattern would allow one subscriber to cancel the operation.
        - A field will be defined to check if cancellation is requested.
        - The field above will be initialized to false.
        - Any event subscriber can change this value to true.
        - One advantage of this approach is that it is not a breaking change.
            - The event subscribers does not know that this cancellation feature is enabled, and change needs to come from the subscriber implementation not the event itself.

    - Second pattern would only cancel the operation if all subscribers agree to cancel said operation.
        - The same field is initialized to indicate that operation should cancel
        - Any subscriber can change this value to express that the operation should continue.
        - The component, however, needs to know if any subscriber has seen an event.
        - If no subscribers are detected, the operation is cancelled immediately.

### Adding another event declaration

- There is another way of declaring an event.
    - Create a backing field for the event
    - Declare the event like a property, with add and remove setters
        - The add and remove act like a get and set but for events exclusively.
        - add will subscribe a component to an event
        - remove will unsubscribe a component from an event

## The Updated .NET Core pattern

- The updated events pattern in .NET Core offers more flexibility, and is also backwards compatible.
- Custom event argument classes does not need to inherit from `System.EventArgs` anymore.

- A custom event argument can also now be a struct.
    - When defining an event argument in a struct, you have to call a parameterless constructor.
        - This ensures that C# would not report that the properties are being accessed before they have been assigned.
    - One exception though is if a custom event argument handles cancellation protocols, the event argument still needs to be a reference type.
        - If the custom event argument becomes a value type, it could never observe any changes made by the event subscribers.
            - A new copy of the argument structure will be copied for each subscriber, and each copy will be different.
            - That will not help for notification if all subscribers rely on one source of truth.

- Backwards compatible:
    - Removing the dependency to `System.EventArgs` will not affect any existing code.
    - Any event argument type created now would not have any existing subscribers in the codebase;
    - Similarly, any new argument types that do not derive from `System.EventArgs` will not break the codebases.

### Events with async subscribers

- As previously discussed, async subscribers will return an async void return type.
- An async event handler cannot return any exception, so it would be a good idea to wrap any major code block in the async event handler with a try catch statement.