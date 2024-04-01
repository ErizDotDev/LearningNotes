# Coding style

## C# identifier naming rules and conventions

### **Naming rules**

- You can use C# keywords as variable names by adding an @ sign on the variable name.

### **Naming conventions**

- Attribute types should end with a suffix `Attribute`.
- C# identifiers should not be prefixed with double underscore characters. This is reserved for compiler generated code.

---

## C# Coding Conventions

### Pascal case

- Name of a class, record, struct, or interface.
- Name of public properties, methods and fields.
- Name of positional properties of a record.

### Camel case

- Private and internal fields
    - Should also be prefixed with an underscore.
    - For static fields, variable name should be prefixed with `s_`

- Method parameters    
- Place using directives outside the namespace.

### Commenting conventions

- Place comments on a separate line.
- Insert one space between the comment characters `//` and the first word of the comment.
- Add appropriate XML comments for any public members.

### Language guidelines

**Implicitly typed local variables**
- Use implicit types when the type is easily verifiable on the right hand side operation.
- Don’t rely on variable name to specify the type of string. Use explicit types.
- Use implicit typing to determine the type in the loop variable.
- Do not use implict typing to determine the type of the loop variable in a foreach statement. (Not easily apparent)

### `try-catch` and `using` statements in exception handling

- Use `using` instead of using `try-finally` statement.
- Also use the new using statement without the parenthesis.

### `&&` and `||` operators

- Use these operators instead of the singular implementation of these operators.
- The double operators short-circuit the evaluation meaning if the first statement evaluates to false, then it will not evaluate the second statement.
- The singular operators will evaluate both the statements which may cause issues if statements are expecting an exception.

### Static members

- Do not make the mistake of naming a static member with a name of a derived class. It can become misleading.

### LINQ queries

- Avoid using singular characters for query variable names, like the one commonly seen on Javascript callback functions.
- Use Pascal casing for the name of anonymous type properties.
- Rename property names if the result of query will result to ambiguous names.
- Use implicit typing.
- Align query clauses under the from clause.
- Use multiple from clauses instead of join when accessing inner collections.