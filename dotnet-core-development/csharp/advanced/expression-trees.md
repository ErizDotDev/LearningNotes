# 🏓 Expression Trees

## Overview
---

- Basically code is represented as data that can be examined, modified and executed.
    - Expression tree allows code to be modified at runtime.
        - Example use case is taking complex algorithms at runtime, do a couple of tweaks in that algorithm and then execute it again on another environment or thread, all at the same runtime.

- Another use case of expression trees is its purpose in the interoperability of the .NET language with other dynamic languages.
    - This is achieved through the dynamic language runtime (DLR)
        - This allows compiler writers to emit expression trees instead of MSIL which is proprietary with .NET languages and frameworks.

- An expression tree is created when a lambda expression is assigned to type `Express<TDelegate>`
    - Expression trees are generated ONLY for a single line lambda.
    - This will not work for multi statement lambdas.

- Expressions are IMMUTABLE.
    - To modify an expression tree, you need to create a new one and then modify the components of the expression tree from there.

### Limitations

- Expression Trees cannot contain async/await functions.
- Any C# features after C# 5 is not written using expression trees.
    - These features are programmed by using existing features that work well with expression trees.

## Expression Trees Explained
---

- Each code written in C# can be sub divided into different nodes of varying hierarchies.
- Consider this program statement for example:

```csharp
var sum = a + b;
```

This expression can be broken down into the following data structure:

- `var sum = a + b`
    - `var sum` - variable declaration
        - `var` - implicit type assignment
        - `sum` - variable name
    - `=` - assignment operator
    - `a + b` - mathematical operation
        - `a` - left operand
        - `+` - mathematical operator
        - `b` - right operand
- The above structure is then packed into the tree data structure that allows for easy traversal of nodes.
    - This explains how the C# compiler analyzes C# programs and creates proper output from a complicated code.

## .NET Runtime support for expression trees
---

- One of the complexities of working with expression trees is that there a lot of possibilities about the type of expression a statement is going to have.
    - Take a variable assignment example, the right hand side operation may simply be a constant, a result of a mathematical operation, a method call, etc.
- The Expression class contains the `NodeType` property that returns an `ExpressionType` type which is an enum of all the expression types available.
    - Once you know the type of node, then you can do all sorts of action depending on the expression type detected.

### Create expression trees

- The Expression library contains a lot of static methods for creating node types that will eventually create the tree.

### Navigating the APIs

- Here are some tips in order to traverse the vast API for expressions.
    - Look at members of the `ExpressionType` to determine possible nodes.
    - Look at static members of the `Expression` class to build an expression.
    - Look at `ExpressionVisitor` class to build a modified expression tree.

## Execute expression trees
---

- Lambda expressions are the only expressions that should be converted to Intermediate Language (IL)
- A delegate value can be assigned to a variable of type `Expression<TDelegate>` where `TDelegate` is the same type as the delegate value being assigned.
- The below statement:
    

```csharp
Expression<Func<int, bool>> expr = num => num < 5;
Func<int, bool> result = expr.Compile();
Console.WriteLine(result(4));
```

The last line of this statement can be written as:

```csharp
Console.WriteLine(expr.Compile()(4));
```

We can remove the assignment portion of the code and went straight to compiling and using the delegate as a normal method.

- Code example for executing expression trees is available on the class file `ExecutingExpressionTrees.cs` at branch `expression-trees`.

### Execution and lifetimes

- The `LambdaExpression.Compile()` expression returns a delegate that can be invoked later.
- There is no need to compile the expression tree each time you want to execute the code it represents.
    - Compiling the same expression tree will generate a delegate that executes the exact same code as expression trees are immutable.

### Caveats

- It should be guaranteed that the variables that would be a part of the delegate should be usable at the location where you call `Compile` and the resulting delegate was executed.
- If the expression accesses a variable that implements `IDisposable`, it is possible that the code might dispose the object while it is still held by the expression tree.
    - Expression trees cannot be used with resources that implements `IDisposable`.

## Interpreting expression trees
---

- .NET Standard has an `ExpressionVisitor` that implements the full expression visitor demo found on the following files at the `expression-trees` branch.
    - `InterpretingExpressionTrees.cs`
    - `ExpressionNodeVisitor.cs`

## Building expression trees
---

- For complex method calls, or methods other than the mathematical operations, a `MethodInfo` object needs to be created first before it can be referenced in a lambda expression.
    - This is demonstrated in the `CreateComplexTrees()` method in class `BuildingExpressionTrees` at the `expression-trees` branch.

## `DebugView` syntax
---

- `ParameterExpression` variable names are prefixed with a $ symbol.
    - Missing variable names for `ParameterExpression` are immediately updated to `var1`, `var2`, etc.

- `ConstantExpression` variable values are displayed directly.
    - For numeric constants, the value is suffixed according with a letter that represents its type.

- `BlockExpression` types are enclosed in angle brackets if its type differs from the original.
    - Otherwise, it will not display the the type.

- `LambdaExpression` are displayed along with their delegate types.
    - Missing variable names for `LambdaExpression` are immediately updated to `Lambda1`, `Lambda2`, etc.

- For `LabelExpression`:
    - If a default value is defined, the value will be displayed before `.LabelTarget`.
    - `.LabelTarget` is the name of the variable that will hold the value.
    - Automatically assigned with the name `#Label1` if variable name is missing.

- Mathematical and logical operators are prefixed with the # symbol.