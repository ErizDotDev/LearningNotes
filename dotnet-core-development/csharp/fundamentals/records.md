# 🏓 Records

- Records are immutable.
	- Properties are set with init accessors rather than the setter accessor.
	- Can also be mutable but records are recommended for immutable data structures.
		- To define mutable records specify properties inside the record with set accessor.
- Unlike classes, overriding the `ToString()` method is unnecessary as the language innately provides a behavior for records to display values of a record’s properties.
- Can inherit from another record but it cannot inherit from a class.

- Has a natural `Deconstruct()` method that breaks down the record properties into individual variables.
	- Needs the out keyword per variable and the variable must match the position of the property for correct value application.
- Can also use the Tuple notation for deconstruction.
### When to use

- Comparing data based on value equality
    - **Value Equality** - Data are equal if they are of the same type, and their property values are congruent with one another.
    - Classes follow the reference equality. **Reference equality** check if both data came from the same reference object.
- If you’re not using Entity Framework Core.

### Review Notes

- On this given link, [https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/records](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/types/records) there is a code block here that looks like this.

```csharp
var phoneNumbers = new string[2];
var person1 = new Person("Denzel", "dela Cruz", phoneNumbers);
var person2 = new Person("Denzel", "dela Cruz", phoneNumbers);
Console.WriteLine(person1 == person2);

person1.PhoneNumbers[0] = "+63 918 123 4567";
Console.WriteLine(person1 == person2);
```

The last line of code will result to True. My initial assumption was that it should be False, because the phone number was only assigned to person1 `PhoneNumbers` object. After diving deep, the reason why the expression reconciled to true is because an array is a reference type, adding to that the same `phoneNumbers` array was assigned to both person1 and person2 records. Any changes made to an array will automatically be copied to any objects that has a reference to that array, simply because an array is a reference type.

- Another code block that I need to review is this

```csharp
Console.WriteLine(ReferenceEquals(person1, person2));
```

This particular expression reconciles to a value of False. Objects person1 and person2 are different objects as displayed on the first screenshot in this section. Both are different instances of the record Person, although both share the same property values. The method `ReferenceEquals()` evaluate if two objects share the same instance or not. Since person1 and person2 are instantiated separately, `ReferenceEquals()` will result to a false value.