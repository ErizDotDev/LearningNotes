# 🏓 Functional Techniques

## Pattern matching

### Important Gotchas

- Craft a switch statement with pattern matching properly
    - Always have a default case in your switch statements.
    - Always leave the default case at the very bottom. The default case is signified by this syntax.
        
        ```csharp
        (sampleCondition) => return true,
        _ => // Do anything you want.
        ```
        
    - If this was not followed, unreachable code warning and error will pop out . Essentially, other cases will never be reached by the code because the default case will always be detected first.
    - The default case, denoted by a discard operator, also includes null values if not explicitly specified as a specific case on a switch statement.

### Null checks

```csharp
int? maybe = 12;

// Feels like a TryParse() wherein a value is assigned to a variable
// if the check is validated to be true.
// This syntax is also known as a declarative pattern.
if (maybe is int number)
{
}

// Meanwhile, his syntax uses a constant pattern.
if (maybe is not null)
{
}
```

### Type tests

```csharp
public T DoFunction(IEnumerable<T> sampleList)
{
	if (sampleList is IList<T> actualList)
	{
		// Do something
	}
}
```

### Compare discrete values

```csharp
public state PerformOperation(Operation command) =>
	command switch
	{
		Operation.SystemTest => RunDiagnostics(),
		Operation.Start => StartSystem(),
		Operation.Stop => StopSystem(),
		Operation.Reset => ResetToReady(),
		_ => throw new ArgumentException("Invalid enum value for command", nameof(command))
	};
```

### Relational patterns

```csharp
string WaterState(int tempInFahrenheit) =>
	tempInFahrenheit switch
	{
		(> 32) and (< 212) => "liquid",
		< 32 => "solid",
		> 212 => "gas",
		32 => "solid/liquid transition",
		212 => "liquid/gas transition"
	};
```

### Multiple inputs

```csharp
public decimal CalculateDiscount(Order order) =>
	order switch
	{
		{ Items: > 10, Cost: > 1000.00m } => 0.10m,
		{ Items: > 5, Cost: > 500.00m } => 0.05m,
		{ Cost: > 250.00m } => 0.02m,
		null => throw new ArgumentNullException(nameof(order), "Can't calculate discount on null order."),
		var someObject => 0m,
	};
```

- For this second example, C# is smart enough to detect which property is being used for comparison. A test may be necessary to know if this will still work if the class has two int properties. Will C# know which property is being used in the comparison? My guess is no but wouldn’t hurt to try.

```csharp
public decimal CalculateDiscount(Order order) =>
	order switch
	{
		{ > 10, > 1000.00m } => 0.10m,
		{ > 5, > 500.00m } => 0.05m,
		{ > 250.00m } => 0.02m,
		null => throw new ArgumentNullException(nameof(order), "Can't calculate discount on null order."),
		var someObject => 0m,
	};
```

### List patterns

```csharp
decimal balance = 0m;
foreach (string[] transaction in ReadRecords())
{
	balance += transaction switch
	{
		[_, "DEPOSIT", _, var amount] => decimal.Parse(amount),
		[_, "WITHDRAWAL", .., var amount] => -decimal.Parse(amount),
		[_, "INTEREST", var amount] => decimal.Parse(amount);
		[_, "FEE", var fee] => -decimal.Parse(fee),
		_ => throw new InvalidOperationException()
	};

		Console.WriteLine($"Record: {string.Join(", ", transaction)}, New balance: {balance:C}");
}
```

To me, this code functions similarly to a Javascript reduce function in that it computes the final value of the variable balance by looping over the list or array output of the method `ReadRecords()`. It then cycles through conditions brought by the switch statement with pattern matching to determine what to do with the values associated with a particular row of data. Different operations are executed for each row pattern on the list or array.

The discard pattern `_` allows us to disregard a singular field from the row that was deemed to be comma delimited. The slice pattern `..` allows us to disregard several fields from the row, with the next parameter being detected by C# if it follows a specific sense of pattern.

### Discards

- Can be used if the value that is the result of the right hand side operation is not needed for the execution of the next line of codes.
- In asynchronous operations, the discards are useful for disposing a value that is returned when a `Task.Run()` operation was executed. It also eliminates the usage of the await keyword for a standalone `Task.Run()`execution.
    - A warning will be displayed for when an await keyword is not present on a `Task.Run()` and a discard operator is also not present.
- Outside a context where a discard operator is supported, the discard operator can serve as a valid variable name.