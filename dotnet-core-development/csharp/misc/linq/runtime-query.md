# Querying based on runtime state

---

- Fundamentally, `IQueryable` has two components:
    - **Expression** - expression tree that represents the components of the current query.
    - **Provider** - an instance of LINQ provider that knows how to convert the query into a set of values.

- In dynamic querying, the LINQ provider stays the same while the expression tree will differ.

- Expression trees are immutable
    - If you want a different expression tree, which is a new query, you’ll need to translate the existing tree into a new one which is also a new query or `IQueryable<T>`.

- The following are techniques for querying differently in runtime:

### Use runtime state from within the [[expression-trees|expression tree]]

- If the LINQ provider supports it, simply use a variable that directly impacts the query, change its value, then re-run the query.
    - These variables may be inside simple methods like Substring, etc.
- The internal expression tree, and the query by extension, will not be modified.
- The only thing that changed is the value of the variable that impacts the query.

### Call additional LINQ methods

- To replace an original query, simply use another LINQ method that will return another `IQueryable<T>` result.

### Vary the expression tree passed into the LINQ methods

- You can pass in different expressions to the LINQ methods.
- You can also compose different subexpressions using a third party library like `PredicateBuilder`.

### Construct an `Expression<TDelegate>` expression tree using factory methods at `Expression`

- This approach is similar to the first approach described here of using a property value that can be changed outside of the query.
- The difference is this will contain a lot more boilerplate code that that will compile a lambda with its different components using the `Expression` class.
- Constructing an `Expression<TDelegate>`
    - `Expression<TDelegate>` inherits from `LambdaExpression`
    - `LambdaExpression` has two components:
    - A parameter list - This is the component of lambda that is enclosed in a parenthesis.
        - Very similar to method parameters
        - Essentially you’re creating an anonymous method.
    - A body - Contains the logic that the lambda will perform.
    - These are the steps in creating an `Expression<TDelegate>`
        - Define `ParameterExpression` for every parameters involved in the lambda.
            - Use the `Parameter` factory method here.
        - Construct the body of your lambda.
        - Wrap the parameters and body using the appropriate `Lambda` factory method overload.
- A utility method that can filter out text from classes that have string properties is available at the `csharp/linq/example-2` branch of the `DC.Lab` project under the class file `CustomerFilterForLinq.cs`

### Add method call nodes to an `IQueryable`'s expression tree

- If the source data is not `IQueryable` instead of `IQueryable<T>` you can wrap the utility method into another expression call that will invoke the resulting lambda from the above utility method.
    - This is needed because `IQueryable` has no access to the generic LINQ methods.

### Construct strings and use dynamic LINQ library.

- Another way to resolve the above issue about `IQueryable` access to generic LINQ methods is to use the [Dynamic LINQ library](https://dynamic-linq.net/) which exposes the same extension methods specifically for the non-generic `IQueryable` and `Queryable` types.