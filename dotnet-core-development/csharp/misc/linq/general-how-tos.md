# General LINQ How-Tos
---

### Return a query from a method

- You can choose to return a query object, instead of the final result of a query.
    - This is for cases where a query needs to be further modified or updated.

### Dynamically specify predicate filters at runtime

- Use `.Contains()` method
- An example use case where this is demonstrated:
    - A query needs to get the students from the data source based on a list of IDs stored in an integer array.
    - The query will return different results if the list of IDs will be changed per execution (per foreach loop)

- Different queries can also be executed at runtime depending on the result of if else and switch statements.

### Handle null values in query expressions

- If the source collection is null or contains an element that has a null value, and your code does not handle null values, a `NullReferenceException` will be thrown.

- Code defensively by handling null values
    - Like a where clause that checks if an element is not null.
    - If the property being used in any LINQ query clause (where, join), cast the value to a nullable type.

- Pattern matching is not recommended to be used in a LINQ query.
    - The query provider may not interpret new C# syntax correctly.
        - A query provider is a library that converts C# query expressions into native data format.

### Handle exceptions in query expressions

- If a method that returns an `IEnumerable<T>` has a side effect of throwing an exception:
    - Define the variable that will hold the value of said method as nullable and provide a null value to start.
    - Wrap the assignment of the variable’s value in a try catch statement.
        - You may choose to handle the exception in the catch statement according to what is right within the context of the application.
        - Most of the time it was a good idea to not handle it.
    - Add an if statement to check if the variable still has a null value or not.

- A foreach loop that unwraps the values inside a query should be placed inside a try catch statement if there is an expectation that the method is called inside the query can throw an exception.

### Count occurrences of a word in another word

- Using the LINQ approach uses more resources compared to using methods suitable for this use case such as `Matches` and `IndexOf` methods.
- Code sample can be found on the `DC.Lab` repository, under branch `csharp/linq/example-2` on class file `WordCount.cs`

### Query for characters in string

- The type string inherits from `IEnumerable<T>` interface, which means any string can be queried as a sequence of characters.
    - This is not the use case for LINQ and should be using Regex instead.
- Code sample can be found on the `DC.Lab` repository, under branch `linq-2` on class file `QueryAString.cs`

### Querying based on runtime state

[[dotnet-core-development/csharp/misc/linq/runtime-query|Querying based on runtime state]]

### Query an `ArrayList` with LINQ

- `ArrayList` are non-generic `IEnumerable<T>` collections so there is a need to explicitly cast what type an `ArrayList` will be processed in the query.
    - This is possible with LINQ by simply specifying the type beside the given data source index variable (beside the keyword from in a LINQ statement)

### Add custom methods for LINQ queries

- Utilizing the [[dotnet-core-development/csharp/programming-concepts/classes-structs-records/methods#Extension Methods|extension method]] pattern.
    - Very useful for creating custom aggregate methods like `Sum`, `Min`, `Max`
        - This custom aggregate methods can be extended with using lambda expressions or delegates as method parameters.
    - It is also possible to create extension methods that will return another sequence.