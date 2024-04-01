# Anonymous Types

- Anonymous types contain public read-only properties.
- An anonymous type can not be null, an anonymous function, or a pointer type.
- Commonly used in conjunction with LINQ to define objects that share similar properties from the base class without referencing the base class or other properties of the base class it does not need.
- Anonymous types works similarly with object types in Javascript/Typescript where a variable can be used to automatically provide the property name as well as the value of said property.

```csharp
var product = new Product();
var bonus = new { note = "You won!" };
var shipment = new { address = "Nowhere St.", product };
var shipmentWithBonus = new { address = "Nowhere St.", product, bonus };
```

- Anonymous types are derived directly from object and it can only be casted to object type.
- Can create new anonymous types with the `with` expression. These demonstrates the concept known as **non-destructive mutation**.
- Comparing two anonymous types, the operation will only return as equals if all their properties are equal.