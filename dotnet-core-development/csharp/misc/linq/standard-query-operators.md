# Standard Query Operators

---

## Overview

- Standard query operators are methods that make up the LINQ pattern.
    - These methods operate on sequences.
    - Contains methods for grouping, filtering, data projection, sorting and more.
- Sequences are object types that implement the `IEnumerable<T>` and `IQueryable<T>` interfaces.
- There are some methods that will operate on types other than `IEnumerable<T>` and `IQueryable<T>`
    - This method will work for non-generic collections such as `ArrayList`.

### Store the results of query in memory

- Queries are lazily executed, meaning the result will only be returned by iterating them in a foreach statement.
- To store the results of a query without the foreach statement, use methods `ToList()`, `ToArray()`, `ToDictionary()` and `ToLookup()`

## Projection Operations

- Projection is the operation of transforming an object into a new form, oftentimes a new type, that only consists of properties and values that are needed and will be used.
- Consists of the following operations:
    - `Select` - most commonly used;
    - `SelectMany` - projects sequences of values from multiple sources (from statements) and flattens them into one sequence.
    - `Zip`
        - produces a sequence of tuples derived from multiple sources.
        - The length of the result of a `Zip` operation will be equal to the length of the shortest provided sequence.
            - C# will omit the last element of the longer sequence as it has nothing to zip with.
        - Can be used with three provided sequences by simply adding the third sequence as a parameter of the `Zip` method.
        - The second parameter of the `Zip` method can accept a `Func<TFirst, TSecond, TResult>` which is used to generate a new sequence based on the two provided sequences.
- The code examples for this section is found in branch `linq-2` at file `ProjectionOperation.cs`
- One key difference between `Select` and `SelectMany` is:
    - Given a class that has a collection property inside it
        - Using `Select` will require two foreach loops to get the values inside the collection property
        - Using `SelectMany` only requires one foreach loop to completely extract the values found inside the collection.

## Set Operations

- Refer to query operations that produce a result set based on the presence or absence of certain elements in one or more given sequences.
- `Distinct` or `DistinctBy`
    - Removes duplicate values in a collection.
    - `DistinctBy` relies on a key to know what values will need to be set.
        - Gets the first instance based on the given key.
- `Except` or `ExceptBy`
    - Retrieves the element from the first collection that is not found on the second selection
- `Intersect` or `IntersectBy`
    - Retrieves the common elements between two input sequences.
- `Union` or `UnionBy`
    - Retrieves the unique elements between two input sequences and combines them into one resulting sequence.
- The By operators operate in such a way that a key needs to be provided in order to produce the desired results based on the operator used.
    - This works best when the target condition is a class property.

## Sorting Data

- LINQ Sorting can take on two sorting parameters

## Quantifier Operations

- Returns a boolean value that indicates if the collection has satisfied the condition specified.
- `All` - checks if all the elements of a sequence satisfies the condition.
- `Any` - checks if one of the elements of a sequence satisfies the condition.
- `Contains` - checks if the sequence contains the specified value.

## Partitioning Data

- Returns a sequence that was split based on the operator that was used.
- `Skip` - removes included elements based on the count/size provided.
- `SkipWhile` - returns a sequence where elements that satisfied the predicate are removed.
- `Take` - returns the elements included on the size provided.
- `TakeWhile` - returns a sequence where elements that satisfied the predicate are extracted.
- `Chunk` - splits the collection into chunks based on the size provided.

## Generation Operations

- Creates new sequence of values
- `DefaultIfEmpty` - replaces an empty collection with a default valued singleton collection
- `Empty` - returns an empty collection
- `Range` - returns a collection of numbers based on the range provided.
- `Repeat` - accepts two parameters, a typed value and the number of types this value will be repeated.

## Equality Operations

- Two sequences that contain two elements that are equal and both have same number of elements are considered equal.
- `SequenceEqual`
    - Compares elements by reference
        - If the sequences contain elements that have equal values, if they have different references like for example, one is an instantiated object while the other is hardcoded or newly defined, it would not be considered equal.
        - To compare based on value and not on reference, declare the `IEqualityComparer<T>` interface on the target class.

## Element Operations

- Returns a single element from a given sequence
- Operations that end with `OrDefault` returns a default value of a given type instead of a null value
- Operations that do not end with `OrDefault` throws an exception when the sequence is empty or null.
- `Single` - Use if you expect the list to have exactly one element.
- `First` - Use if you have multiple instances of a list element (for example: records with same date) but you only just need the first element out of all the instances.

## Converting Data Types

- `Cast<T>` - casts the type of elements of a non-generic collection into a specific type.
- `AsEnumerable()` - cast the type of sequence as `Enumerable<T>` given that the class inherited from the `IEnumerable` interface.
- `AsQueryable` - same logic as `AsEnumerable()`
- `OfType()` - filters the sequence of a non-generic collection
- There are also methods for converting the `IEnumerable<T>` or `IQueryable<T>` to generic collections.
    - This is to save the results of a query and loading them into memory.

## Concatenation Operations

- Combines the elements of two sequences
- Uses the `Concat` method

## Aggregation Options

- Contains operators that do certain calculations on the sequences and collections to derive a single value result.

## Join Operations

[[dotnet-core-development/csharp/misc/linq/join-operations|Join operations]]

## Grouping Data

### Group query results

- The query variable type that stores the a group query is `IEnumerable<IGrouping<T1, T2>>`
    - This type contains a Key property which is the identifying property for that grouped collection.
    - The type of the key is the T1 generic type in the above notation.
- You can read a bit about `IEnumerable<IGrouping<T1, T2>>` here → [[miscellaneous#`IGrouping<T1, T2>`|IGrouping<T1, T2>]]
- Code can be found on the `GroupQuery.cs` file on the `linq-2` branch of the `C#-Lab` repo.

### Create a nested group

- To unwrap values inside a nested group will take 3 foreach statements.

### Group results by contiguous keys

- This demo exhibits that a LINQ operation can be thread-safe when used with a mutable data source, which is always a cause for concern in multi-threaded environments.
- It becomes type safe by making a copy of each group or chunk as the source sequence is iterated.

