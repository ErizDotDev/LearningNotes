# **Query Expression Basics**

---

### What is a query and what does it do?

- “A query is a set of instructions that describes what data to retrieve from a given data source (or sources) and what shape and organization the returned data should have.”

- In an application’s perspective, it does not care what is the type of data and the structure of the data when it comes in.
    - Technically, the application will see the data as an `IEnumerable<T>` or `IQueryable<T>`

- Given the above data sequence, a query can do the following:
    - Fetch a subset of a data following a certain criteria without modifying the contents of that collection or sequence.
    - Get a sequence of elements but transforms the data to be of specific organization as specified in the query.
        - This scenario covers transformation of data from an object into an XML format for instance.
    - Get a specific data that is inferred from the given collection like getting a count of certain elements.

- It is good practice to separate the query expression from the method expression to make it more readable.
    - Place the result of a query expression in a variable.
    - Use the created variable for the method expression.

### What is a query expression?

- A query expressed in a query syntax.
    - Characterized with the use of a series of clauses to make it as human readable as possible.
        - It feels like reading a sentence, albeit an incoherent one.

- Rules:
    - Must begin with a `from` clause
    - Must end with a `select` or `group` clause
    - Between the `from` clause and the last `select` or `group` clause, the following clauses can be used to manipulate the data.
        - `where`, `orderby`, `join`, `let`, additional `from` clauses.

### Query variable

- A query variable, is simply, a variable that holds the query instead of the result of the query.
- The results of the data are generated in a foreach loop.
    - What the query variable holds is the collection that will be placed in the foreach loop.
- Regardless of whether the syntax used was a query syntax or a method syntax, the variable that hosts these expressions are a query variable.
- If the expression inferred a specific data and used methods to infer such data (`Count()`, `Max()`, `Min()`), then it is not a query variable anymore.
    - What these variables are storing are the result of a query, not the query itself.

### Explicit and implicit typing of query variables

- Both are possible.
    - We can use the `var` keyword to tell the compiler to infer the type of the variable at compile time.

### Starting a query expression

- A query starts with a from expression declaring the data source to which the data will come from.
- If the data will be filtered, a range variable might be defined.
    - The range variable is strongly typed, meaning it will reflect the underlying data type of the collection from the data source.
        - The dot operator can be used to access the members of said type for its range expression.
    - This variable will be in scope until the expression is terminated with the semi-colon, or with a continuation clause.
- A query can contain multiple from expressions.
    - A scenario where this may be useful is when iterating through a list of Country object for the first from expression.
    - Then the country object has a list of City object. This can be accessed using the second from expression.

### Ending a query expression

- `group` clause
    - Use this to produce a sequence that is grouped based on the key defined in this clause.
    - The key could be any member of the type defined in the collection.
- `select` clause
    - Use this to create a new sequence with only the selected properties in the collection.
    - This can also be used to transform existing data into data of new types, also known as _projection_.
    - If defining an anonymous type, it should always have the new keyword as this is a new object that is being defined.

### Continuations with into

- This is used in a scenario where further manipulations are needed to be done on a query result.
- The into keyword will hold the result of the initial query result under a defined name.
    - This name can then be referenced further down the query for the additional transformations.

### Filtering, ordering, and joining

- Works awfully similar with their SQL query counterparts.
- To specify additional where clause statements, instead of using the keywords `AND` and `OR`, their bitwise counterparts `&&` and `||` are used instead.
- A `let` keyword can also be used with LINQ!
    - This is used to store the result of a transformation of a field into a variable
    - This variable can then be projected in the `select` clause.

### Subqueries in a query expression

- Also possible.
- The subquery will start again with another `from` clause.
    - The main query and the subquery doesn’t necessarily have to share a similar `from` clause.