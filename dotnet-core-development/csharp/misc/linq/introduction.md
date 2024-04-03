# Introduction to LINQ Queries
---

## Three Parts of a Query Operation

1. Obtain a data source.
2. Create the query.
3. Execute the query.
    - Loading the result of a query (an `IEnumerable<T>` or a `IQueryable<T>`) in a foreach loop demonstrates this part.

- The steps of creating a query and executing a query is different.
    - Creating a query does not mean executing the query.
        - Once a query is created, no data is being retrieved yet.
        - Executing a query means getting the data.

## The Data Source

- Types that support `IEnumerable<T>` or a `IQueryable<T>` are _queryable types._
    - `IEnumerable<T>` is the interface that enables for data from generic collections to be fetched in an iterator, or foreach statement.
        - Both generic and non-generic (`ArrayList`) collection support `IEnumerable`.

- If the data source is not stored in the memory already (example: data that are still in a physical file format like XML, Excel, etc.), needs to be extracted first from its source.

- There are various tools that convert data from a specific source into a queryable type. One example:
    - LINQ to SQL - tool that converts SQL data into a type that LINQ can work with.

## Query Execution

### Deferred Execution

- When a query variable is created, what is stores is not the data produced by the query, but the commands that will execute a query.
- A query will only be executed once the query is iterated over in a foreach statement.
    - At this point, the query results were also retrieved.

### Forcing immediate execution

- Queries that perform aggregation function, or operations that get a singular scalar value or data from the query, demonstrates the immediate execution feature of LINQ.
    - Some example of aggregate functions are `Count`, `Max`, `Min`, and many more.
    - These functions return a single value, not a queryable type.

- Calling the `ToList()` and `ToArray()` methods caches the result of the query.