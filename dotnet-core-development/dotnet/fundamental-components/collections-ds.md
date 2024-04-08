# ⭐ Collections and data structures
---

## Overview

### Common features

- **Ability to enumerate a collection**
    - Through implementing `System.Collections.IEnumerable` or `System.Collections.IEnumerable<T>` which hides the complexity of enumerating logic brought by the above interfaces.
    - The result of using the above interfaces is a query object that can be further filtered/processed/manipulated or queried using LINQ.

- **Ability to copy contents of a collection to an array**    
    - Can be copied using the `CopyTo` method which results in a 1D array that starts with the lower bound of 0.
    - Copy order is based on the sequence the enumerator returns them.

- Most collections will have the following features:    
    - **Properties to get a collection’s count or capacity**
        - *Capacity* refers to the number of elements that a collection can contain.
        - *Count* refers to the actual number of elements a collection contains.
        - The modern design of C# collections involve automatically growing the capacity of the collection when the initial capacity is reached.
            - Its benefit lies with the developers not having to write code to handle the scaling of the collection’s capacity.
            - However, this design results in a performance hit if the capacity expands to its limit which means a new array will be created to transfer the current elements of said array as well as increase its capacity to account for the new elements that are to be added.
                - This results to an O(n) operation - meaning the time it takes to complete the operation depends on the size of the input.
            - Inversely, if the capacity is greater than the actual count of the collection, the time it will take to complete the operation of adding an element to the collection is constant - or is not affected by the size of the input. (0(1))

    - **Consistent lower bound**
        - C# collections all maintain a lower index value of 0 which is the start value an element of a a collection can be accessed.

    - **Collections are synchronized so that multi-threaded environments can safely access the collection elements.**
        - Non-generic collections are not thread-safe by default.
        - Use collections in `System.Collections.Concurrent` to implement thread-safe collections.
            - An alternative to this approach is using immutable collections.

### Algorithmic complexity of collections

- `List<T>` is efficiently enumerated using a `for` or a `foreach` loop.
    
- `ImmutableList<T>` is only efficiently enumerated using a `foreach` loop.
    - Under the hood, `ImmutableList<T>` uses a binary tree to store data unlike `List<T>` that uses an array.
    - Because of this, the algorithmic complexity of enumerating data in `ImmutableList<T>` and by extension the binary tree is `O(log(n))`.

- `SortedSet<T>` has the same time complexity as `ImmutableSortedSet<T>` because both collections use binary tree under the hood.
    - The only difference is that `ImmutableSortedSet<T>` implements an immutable binary tree.

---

## Selecting a collection class

- Ask the following questions:
    - Do you need a sequential list where an element can be discarded after its use?
        - Consider using `Queue<T>` for FIFO, `Stack<T>` for LIFO.

    - Do you need to access the elements of a collection in a specific or random order?
        - `Queue<T>` and `Stack<T>` again are the answers here, with the addition of `LinkedList<T>` for random access of data.
            - A `LinkedList<T>` can access data from either direction, from head (first element) to tail (last element) or vice versa.

    - Do you need to access an element by index?
        - Use `List<T>` to access its element using zero-based index.
        - Use `Dictionary<TKey, TValue>` and `SortedDictionary<TKey, TValue>` to access its elements using the key of the element.
        - `KeyedCollection<TKey, TItem>` and `SortedList<TKey, TValue>` uses both zero-based index and key to access its elements.

    - What kind of value will an element of a collection contain?
        - For single value, use `IList<T>`
        - For a value partnered with a key, use `IDictionary<TKey, TValue>`
        - For a single value partnered with an embedded key, use `KeyedCollection<TKey, TItem>`
            - An embedded key is a key that is derived from one of the properties contained in the value or the item. ^47f83c
        - For one key but multiple values, use a `NameValueCollection`

    - Do you need to sort the elements differently from how they were entered?
        - `HashTable` sorts the collection based on the hash codes of its elements.
        - `SortedDictionary<TKey, TItem>` sort the elements by their key.
        - `List<T>` has a `Sort` method that expects an `IComparer` implementation as parameter to define how the collection will be sorted.

    - Is the speed of retrieving an item a factor?
        - `ListDictionary` is effective if the items in the collection are less than 10.
        - `Dictionary<TKey, TValue` is faster than `SortedDictionary<TKey, TValue>`

    - Do you need a collection that only accepts strings?
        - `StringCollection` (based on `IList`) and `StringDictionary` (based on `IDictionary`)
        - Almost all other generic collections.

- Note that the recommendations above also considers the non-generic and immutable and thread—safe variants of the collection.
    - It was omitted for brevity.

---

## Other collections

- `BlockingCollection<T>` implements a collection class with bounding and blocking functionality.
    - Commonly used for producer and consumer patterns.
    - Useful for tasks involving parallel processing and multi-threaded environments.
- `ConcurrentBag<T>` provides fast insertion and removal of elements.
    - Again, it deals with parallel processing and multi-threaded environments.

### Immutable builders

- Collections in `System.Collections.Generic` has a Builder feature where the items can be added in the immutable collection by:
    - Adding the items first when the collection is in a “build” or mutable state.
        - This is achieved by calling the non-generic `CreateBuilder()` method.
    - After calling the `ToImmutable` method, only then will the collection be frozen and turn into an immutable collection.

### Additional functionality

- Most collections accept delegates as parameters to perform filtering and data manipulation operations.
    - `Predicate<T>` for searching the list
    - `Action<T>` to perform an action for each element on the list.
    - `Converter<TInput, TOutput>` to convert the elements’ type into another type.
    - `IComparer<T>` to provide a custom sorting logic to the collection.

---

## Comparisons and sorting in collections

### Equality checks

- The methods `Contains`, `IndexOf`, `LastIndexOf`, and `Remove` uses equality checks.
- Generic collections follow the following rules for equality checks:
    - It checks if the provided type implements `IEquatable<T>` and its implementation of the `Equals` method.
    - If the type does not implement the `IEquatable<T>` interface, it does a reference equality check using `Object.Equals()`.

### Sorting

- Methods `BinarySearch` and `Sort` uses an ordering comparer for sorting.
    - Comparisons can be between the elements of the collection or compared against a specified value.

- Be aware of the concepts `default comparer` and `explicit comparer` when comparing objects.
- Default comparers rely on the types implementing `IComparable` or `IComparable<T>`
    - `IComparable` is different from `IComparer`
        - `IComparer` is the underlying interface used to provide the delegates for the sort methods.
        - `IComparable` is the interface used to indicate that the type can be sorted using a default logic implemented in the `CompareTo` methods from the interfaces.
    - It’s good practice for types to implement these interfaces especially when these types are going to be used in a collection where sorting is absolute necessary.
    - If the types don’t implement either interfaces, a delegate should be provided to the `Sort` methods.

- The current culture setting impacts the sorting that is done.
    - By default, sorting is culture sensitive.
    - To turn this off, pass in the parameter `InvariantCulture` for methods that accepts `CultureInfo` as parameter.

---

## Sorted collection types

- `SortedList`, `SortedList<TKey, TValue>`, `SortedDictionary<TKey, TValue>`
- `SortedDictionary<TKey, TValue>` uses more memory than both sorted list collections.

---

## Thread-safe collections

### Disadvantages of regular and generic collections

- Regular collections `ArrayList` and `HashTable` provide some thread safety using the `Synchronized` property that creates a thread-safe wrapper around the collection.
    - This means the collection is locked when an item is being added or removed, and each operation must wait for its turn to unlock the collection.
        - This is a performance pitfall especially for large collections.
        - This design isn’t protected from race conditions.
- Generic collections don’t provide thread synchronization.

---

## `HashSet<T>`

- Unsorted collection that contains no duplicates.
- Its capacity increases as objects are added to it.
- Has similar performance with `HashTable`
    - Like `HashSet<T>`, `HashTable` does not allow duplicate keys.

### HashSet and LINQ operations

- `HashSet<T>` has its own operations outside of LINQ.
- The main difference between the `HashSet<T>` operations and the LINQ operations is that LINQ operations generate a new `IEnumerable` object for the results whereas `HashSet<T>` operations modify the current collection.

---

## `KeyedCollection.ChangeItemKey`

- This method is used to update the key in a keyed collection.
- Keyed collection’s keys are [[collections-ds#^47f83c|embedded]].
- Using this method will not update the value of the embedded key.
    - It just updates the key on the lookup dictionary, an internal data structure that stores the key and value relationship.
- This means that if the embedded key is used to retrieve a value, there will be no results, because the key is updated in the lookup.

