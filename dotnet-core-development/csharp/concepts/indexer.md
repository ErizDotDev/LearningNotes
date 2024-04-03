# 🏓 Indexer

---

- This feature allows modeling of objects that function as list, dictionary, or some other storage type which can be accessed by one or more arguments.
- In its implementation, an indexer is usually defined inside an object together with a collection, may be an array, a list or dictionary.
    - However, there are cases where collections are unnecessary since the value can be computed immediately based on the arguments provided.
- Indexer can be used as an abstraction to access data inside said collection without defining new methods and such.
- Enables properties that are referenced by one or more arguments.
    - The arguments provide an index into some collection of values.
- Simplifies the syntax for calling the list property from `float temp = tempRecord.temperatures[4]` to `float temp = tempRecord[4]`

### Indexer Syntax

- Declaring an indexer is like declaring a property.
    - The key difference is that indexers will have a this keyword as the main name followed by square brackets indicating the key name used to identify an indexer.
    - Here’s an example:

```csharp
public int this[string Key]
{
	get { return storage.Find(Key); }
	set { storage.SetAt(Key, value); }
}
```

- The rules for the getter and setter of indexers is also similar to that of the properties in that,
    - It allows for multiple access modifiers per getter or setter;
    - Can specify read-only and write-only indexers;
- Auto implemented properties are not a thing with indexers
    - Compiler cannot always generate a location for the storage of an indexer.

### Scenarios

- Most common scenario is when your type models an array or a vector.
    - When your type models historical data, the data can grew into a size that cannot be loaded into the memory all at once.
    - You need to section your data in order to load it in its entirety.
    - Creating an array indexer is like defining your model is a list or an array.

- Another scenario is when you model a dictionary type - a storage model that stores values and keys to said values.

- Can also used in a multi-dimensional map.
    - Allows for scenarios where:
        - All the arguments provided are of the same type;
        - Arguments are of different types.
    - The first example used to demo this particular scenario feels a lot like a computed property that allows for two arguments to reach a particular computation value.
        - This particular example did not use any underlying collection, as a value was reached through sheer computation with the aid of the provided values.
        - The demo in this example may not have used a collection but it entails some kind of loop to access the necessary data for its computation.
    - The second example used a collection to store the data.

### Behind the Scenes

- Compiler generates an Item property (or an alternatively named property defined using the `IndexerNameAttribute`

### Rules

- Index can be of other types aside from `int`. e.g. string
- Set the accessibility of the getter and setter as strict and as reasonable as possible.
- Apply an error handling strategy for when the client code passes an invalid index value.

### Indexers in Interfaces

- Indexer implementation can be specified in an interface.
- In an interface, there are no access modifiers and the accessors do not have a body.
    - The implementation will specify the accessibility and the implementation of the accessor.
- Explicit interface implementation can be applied with indexers as well, if the class implementation inherits from multiple interfaces, all of which have indexer contracts to implement

### Interesting Features not covered by Indexers

- Aliasing
    - allows me to reference the alias name in the class, while that alias pertain to a long type definition for a Dictionary.
- Strip off the time from a `DateTime` object using this declaration: `DateTime.Date`
    - I think this zeroes out the hour, minute and second values.
    - Helps with date comparisons.