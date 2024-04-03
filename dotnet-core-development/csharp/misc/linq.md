# Language-Integrated Query (LINQ)

## Overview of LINQ
---

- Available syntaxes for LINQ: 
    - _query syntax -_ recommended for common use
    - _method syntax_ - represented by chaining LINQ methods to derive to a particular result.

- Multiple `from` LINQ operations represents a select many operation where the elements found in the first from operation will be combined with the elements found in the second from operation.
- **Terminal methods** in LINQ are the last methods to run in a LINQ method chain.
- LINQ queries are statements that are evaluated lazily. Lazy evaluation means that a statement will not be executed until it is needed.
- Lazy evaluation is the better choice for when the data source is a separate process, like a database engine for example.

- LINQ is a name of a group of technologies that integrated querying into the C# language.
- It resolves some of the issues related to querying:
    - Type safety of data being queried
    - No unifying language for querying different data sources (SQL databases, XML files, etc.)
- Introduced the query syntax which helps developers perform data manipulation (sorting, filtering, grouping, ordering, etc.) on various data sources.

### Getting Started with LINQ in `C#`

[[dotnet-core-development/csharp/misc/linq/introduction|Introduction to LINQ Queries]]
[[dotnet-core-development/csharp/misc/linq/query-expression|Query Expression Basics]]
[[dotnet-core-development/csharp/misc/linq/type-relationships|Type relationships in LINQ]]

### Standard Query Operators

[[dotnet-core-development/csharp/misc/linq/standard-query-operators|Standard Query Operators]]

### LINQ to objects

- The benefits of using LINQ queries over foreach loops are:
    - Expressive and easy to read code, especially when filtering with multiple conditions;
    - Has built in methods for various operations such as grouping, joining, and ordering data among many others
    - Can be used in other data sources with no issues

- Classification of standard query operators by manner of execution
    - **Immediate**
        - This means the data source is read and the operation is performed once;
        - Characterized by the use of aggregation functions such as Sum, Max, Min, etc.
        - Also characterized by converting collection into lists, arrays or dictionaries.

    - **Deferred**
        - Operation is not performed when the code for the query was declared.
            - It was performed when the query variable is enumerated, by using a foreach statement.
        - Almost all LINQ operations are using deferred execution.
        - Streaming
            - This means that they do not have to read all the source data before they yield elements
        - Non-streaming
            - Operations must read the data all at once before yield a result element.
            - Sorting and grouping operations are examples of non-streaming operations.

### General LINQ How-Tos

[[dotnet-core-development/csharp/misc/linq/general-how-tos|General LINQ How-Tos]]

### Write LINQ queries in `C#`

- To work with both the query and method syntax of LINQ, just enclose the query syntax follow by the dot operator, then the method option of choice.
    - However it is advised to split the definition of the query and method syntax to avoid confusion.
