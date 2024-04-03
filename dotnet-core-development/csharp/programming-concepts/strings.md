#  ⭐ Strings and string literals

---

- String, behind the scenes, are stored in a sequential read only collection of char objects.
    
- There are no null terminating characters in C# which means C# strings can have multiple embedded null characters.
    - A null terminating character signifies the end of a string in other programming languages.
    - An embedded null character, or referred to simply as “null character” is a character in computer programming that has a numeric value of 0.
        - A null character is useful in C# for data normalization and formatting purposes.
        - 
- The `Length` property of string refers to the number of `char` objects in the collection.
    
- `string` and `System.String` are similar.
    - `string` is just used as an alias for `System.String` which means the type can be used even without referencing the System namespace.

### Immutability of strings

- Strings are immutable objects.
- In a scenario where a value is reassigned to a string variable, what happens internally are:
    - A new string object is initialized with the new value of the string
    - This new string object is then assigned to the variable.
    - The old string object containing the old value of the variable is released for garbage collection.

### Quoted string literals

- The standard string value syntax.
- Can include escape characters such as new line or tab.

### Verbatim string literals

- Denoted by the use of the `@` syntax at the start of the string.
- Uses the actual state of the string for its value, retaining nuances such as extra whitespaces, line breaks and tabs.
- Very useful for texts in multiple lines.
- In this mode, double quotes can be part of the string by using the double quotation mark immediately followed by the quotation mark of choice. e.g. `This is an ""emphasized text"".`
    - To insert a quotation mark on a regular string literal, what is normally used is the syntax of backslash followed by the quotation mark of choice. e.g. `This is an \\"emphasized text\\".`
- Can be combined with string interpolation.
    - Syntax can be `$@` or `@$`

### Raw string literals

- Introduced recently in C# 11, it is a new way of defining raw string literals.
- Denoted by the use of the triple double quotation marks at the start and end of the raw literal string.

- Have specific rules that needs to be followed:
    - Single line raw literal string must have the opening and closing triple double quotation marks in a single line.
    - Multiple line raw literal string must have the opening and closing triple double quotation marks in their own lines.
    - Multiple line raw literal string must have all of its line following the same indentation or the compiler will throw an error.

- This makes it easier to generate complex string literals such as a formatted JSON string for example.

- Can be combined with string interpolation.
    - If the raw literal string will include `{}` and the $ characters, add another $ at the start of the string as this signifies how many levels of `{}` character it needs to count in order to properly render the literal curly brace characters with the string interpolated value.

[List of string escape sequences](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/strings/#string-escape-sequences)

### Accessing individual characters

- `StringBuilder` can be used to modify characters in a string in place, instead of creating a new string.
    - This feature is not available in the built-in string type.

### String Interpolation

- You can set the width of an interpolated text.
- The `$"{value}"` notation accepts a second parameter where you can declare the spacing assigned after particular text.
- Particularly useful for creating tables in the CLI.
- To escape curly braces in string interpolation syntax, simply use this notation, `{{}}`
- To use a ternary operator inside a string interpolation syntax, enclose the ternary statement inside a parenthesis.
- You can assign interpolated strings into type `System.FormattableString` to produce strings that are consistent with a selected Culture.