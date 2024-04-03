# Distinguishing delegates and events

---

- Often a hard task because delegates and events are similar.
    - Hard to decide which one to use as well.

### Listening to events is optional

- If your code must call the code supplied by the supplier, then use delegates.
    - Example: LINQ statements - A callback must be defined to know what way a list should be sorted.
- Otherwise, if your code can complete without calling any subscribers, then use events.
    - Example: UI events - They still complete even without any subscribers to these events.

### Return values require delegates

- Events have void return types.

### Events have private invocation

- Only classes that contain the event definition can invoke said event.
- The other classes can only add and remove event listeners.

### Event listeners often have longer lifetimes

- An event source will be raising events for long periods of time.
- Unlike the delegates whose function only lasts until the method is called. After a method returns, it may not be used again.