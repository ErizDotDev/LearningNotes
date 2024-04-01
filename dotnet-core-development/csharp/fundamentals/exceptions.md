# Exceptions and errors

### Using exceptions

- A try statement can have multiple catch statements.
    - Catch statements, in this case, should be ordered such that the most specific exception comes first, then the least specific exception comes last.
- There is a exception filter feature with which you can add a boolean condition beside the exception using the when keyword. This will allow for multiple actions be applicable to the same exception with different monitored values.

### When to use exceptions

- Parameter of a function has an invalid value.
- Inappropriate function call is made based on the object state. (example: Attempting to write on a file that is on a readonly state)
- Method argument causes an exception.

### Things to avoid

- Don’t use exceptions as part of a program’s logical flow.
- Exceptions should not be a return value.
- Don’t create exceptions that can only be thrown in debug mode but not in release mode.

### Defining Custom Exception Classes

```csharp
[Serializable]
public class InvalidDepartmentException : Exception
{
	public InvalidDepartmentException(): base() { }
	public InvalidDepartmentException(string message): base(message) {}
	public InvalidDepartmentException(string message, Exception inner): base(message, inner) {}
}
```