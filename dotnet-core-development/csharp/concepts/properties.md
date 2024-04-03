# 🏓 Properties

---

```csharp
public class Person
{
	public string? Name { get; set; }
}
```

- The syntax above is known as _auto property syntax._
- Compiler generates a storage location for the field that backs up the property.
    - By practice, we define a private field that will hold the property value.
    - The syntax below is how we do it before.

```csharp
public class Person
{
	private string? _name;

	public string? Name { 
		get { return value; } 
		set { _name = value; } 
	}
}
```

- The private field `_name` is what is also known as _storage_.
- The property `Name` is also known as a _backing property_.

### Access Control

- Properties that have a private set means that assigning a value to that property can only happen in the class that contains said property.

### Init-only

- _[[classes-structs-records#**Object Initializer**|Object initializer]]_ is a way to define property values without invoking a constructor. Here’s an example.

```csharp
var person = new Person
{
	Name = "Denzel"
};
```

- Object initializer will not work if you define a class property that only has a get accessor.
- To make object initializers work, the `init` successor should be used instead of set.
- The `required` keyword can also be used to define a property with the `init` accessor.
    - This will require any newly initialized Person object to set a value for that property.
- An attribute, `SetsRequiredMembers`, can be placed on a constructor to let the compiler know that all required properties should be set.

### Cached evaluated properties

- This is characterized by combining the concepts of having a private field, or storage, and computed properties.
    - Inside a get accessor, storage will be checked if it has a value or not.
        - If not, a new value will be computed and saved into said storage. The value will also be returned by get.
        - Here is an implementation of cached evaluated properties.
            
```csharp
public class Person
{
	private string? _firstName;
	public string? FirstName
	{
		get => _firstName;
		set
		{
			_firstName = value;
			_fullName = null;
		}
	}

	private string? _lastName;
	public string? LastName
	{
		get => _lastName;
		set
		{
			_lastName = value;
			_fullName = null;
		}
	}

	private string? _fullName;
	public string FullName
	{
		get
		{
			if (_fullName is null)
				_fullName = $"{FirstName} {LastName}";

			return _fullName;
		}
	}
}
```

   - This is used so that properties are computed only once.

### Attaching [[reflection-attributes#**Attributes**|attributes]] to auto-implemented properties

- Attributes can only be attached on fields, not on backing properties.
- To use said attributes on properties, add the `field:` prefix then place the name of attribute. Here’s an example

```csharp
// Ommited for brevity

[field:NonSerialized]
public int Id { get; set; } = 0;

public string Name { get; set; } = string.Empty;
```

### Implementing `INotifyPropertyChanged`

- When the value of property changes, the object raises the `INotifyPropertyChanged.PropertyChanged` event.