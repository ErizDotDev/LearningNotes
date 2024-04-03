# Type relationships in LINQ
---

```csharp
List<string> names = new List<string>("Denzel", "Mike", "Harvey");

IEnumerable<string> namesQuery = from name in names
								where name[0] == 'M'
								select name;

foreach (string str in nameQuery)
{
	Console.WriteLine(str);
}
```

- Operating in LINQ ensures type safety across its usage, from defining the data source, to defining the query, and fetching data from said query.
    
- In the example above, type relationships in LINQ is demonstrated by the following:
    - The source definition is a generic collection of a particular type. With the above example, the type in question is string. The data source is a list of string values.
    - The variable in query, particularly the variable `name` that is extracted from the names list variable, reflects the singular type from the source collection which is `List<string>`. Therefore, the type of `name` is `string`.
    - The query returns the name variable as well which is of type `string`. But the result type is a generic collection, `IEnumerable<string>`. The query collates all the values of the query of type string, thus it makes sense that the query will return a collection of values as well of type string.
    - When getting the data from the query, type safety is maintained by making types consistent across the board.
        - In the example, the value extracted from the `namesQuery` is of type string, the variable used to represent that singular value is of type string as well.

- There are a couple of scenarios that makes this concept a bit more challenging. Assume that the collection is of type `Customer`, which is a class type that contains a couple of properties.
    

```csharp
List<Customer> customers = new List<string>(
	new Customer("Denzel", 28),
	new Customer("Mike", 32),
	new Customer("Harvey", 40)
);

IEnumerable<string> oldCustomersQuery = from customer in customers
										where customer.Age > 30
										select customer.Name;

foreach (string str in oldCustomersQuery)
{
	Console.WriteLine(str);
}
```

- With the modified example, there is still a pattern of consistency here for this to still be valid LINQ operations.
    - The query definition expects a value with a singular type of string. The way this works is that the query returns a string value, which is the customer’s name.
        - Even though the data source is of type Customer, data can still be extracted from it and will qualify as a valid value being returned to the query result variable.
    - If we returned the customer’s age instead, the compiler will return an issue as the type explicitly mentioned that it expects a string type.
    - This will be a different scenario if the type was implicitly defined.
        - If we use `var` instead of the expected type definition, the compiler will infer the type that will be returned by the query, by looking at the type that is found on the select operator.