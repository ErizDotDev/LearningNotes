# Methods

---

[[dotnet-core-development/csharp/concepts/methods|More information about methods]]

## Returning a value by reference

- `ref` keyword is added to the method definition to use this feature.
- The `ref` keyword was also added after the `return` keyword.
- Methods returning by reference should not have the return type `void`
- This approach is commonly used for performance optimizations, and enabling the use of mutable types in code.
- This approach, also, must be used with extreme caution as it can cause issues relating to side effects of mutability, and concurrency issues (race conditions).
    - In short, you must know what you’re doing if you want to utilize this approach.

## Local Methods

- Methods that are nested in another method or object member.
    - Only the method where they are nested can call these methods.
- When defining a local method argument list, refrain from using the keyword `value` as argument name as the compiler uses this keyword as a temporary variable name.
    - This variable contains variables from the method where the local method is contained.
        - This includes method parameters, and all local variables in that method.
- Local methods cannot contain accessibility modifiers as they are all private by default.
- Starting in C# 9.0, [[reflection-attributes#**Attributes**|attributes]] can also be applied to local methods and its method parameters.
- Local methods can act as iterators.
- Code samples demonstrating local methods is available in the `local-functions` branch of the `CSharp-Lab` solution.
- The benefits of using a local method is it can encapsulate pieces of functionality into a unit where it is exposed only to the method where it is used.
	- Can be a bit of a waste to define a method in a class where it is only used by another method.
- 
- **Static local functions**
	- Use the `static` keyword if you don't want the local function to access variables on the parent method.
	- Use this approach to prevent side effects on your methods.

### Exceptions

- Implementing local methods allow exceptions to surface immediately.

### Vs. Lambda expressions

- Although they may look similar, local functions and lambda expressions have key differences to watch out for and be aware of:
    - Local functions are defined as normal functions; Lambda expressions are assigned in variables of type `Action` or `Func`.
    - Lambda expressions rely on type arguments supplied in its type to determine the argument and return types; Local functions do not, because they are defined like normal functions.
    - Because lambda expressions are declared and assigned at runtime, the order in which the statements appear in the method for declaring, assigning and calling the lambda expression is set in stone. This means an exception will be thrown if these steps are rearranged in any order.
        - For local functions, rearranging the return statement and the local function definition is fine because local function is defined in compile time.
        - Because of the difference of the two, recursive algorithms perform better with local functions.
            - Local functions just needs to be called while lambda expressions have key steps that must be maintained in order to define, assign and call them.
    - Lambda expressions are converted to delegate upon declaration. Local functions are flexible that they can be written like a normal function or as a delegate.
        - A local function is converted to delegate **ONLY** when used as a delegate.
    - Both local function and lambda expression follows the rules of definite assignment - where a function can access and manipulate variables in the outer function or the enclosing function.
        - If a local function uses a variable from the outer function, the local function is implemented then as a delegate type.
    - A local function can use a class and struct for the [[dotnet-core-development/csharp/programming-concepts/classes-structs-records/methods#^f9967f|closure]]. A lambda expression can only use classes.

### Heap Allocations

- Local functions can help avoid lessen the amount of heap collections, which are always necessary for lambda expressions.
    - This is only applicable if the local function is never converted into a delegate type, which is possible in other usage scenarios.
    - Lambda expressions need to be instantiated which means extra memory allocation - an additional performance overhead.
- One trick to use to avoid converting a local function to a delegate is to mark the local function as static.

### Implicitly Typed Local Variable

- `var` is not a type, but an instruction to the compiler to infer and assign the type of the right hand operation.

## Extension Methods

- Extension method is a way to implement new methods for existing types without creating a new derived type, recompiling or modifying the original type.

- Extension methods can only extend a class or interface method, not override them.
    - If an extension method has a similar class method with the exact same method signature, the instance method will take precedence over the extension method.
        - Extension method is low in the priority of methods called by the compiler, following the instance methods.

- Extension methods are commonly used in the following scenarios:
    - Implementing collection functionality;
    - Implement layer specific (reference to Onion Architecture) functionality
        - This pertains to methods that are used by a single layer but are unwanted in other layers.
    - Extending pre-defined types.

- There should be no issues in terms of security when it comes to using extension methods to impersonate existing methods.
    - This is because the name collisions are resolved in favor of the instance methods instead of the extension methods.

### Using extension methods to modify structs

- It is difficult to utilize extension methods to extend struct types because they’re passed by value to the methods.
    - This means any changes to the struct are made to a copy of the struct.
    - To bypass this, use the `ref` keyword on the extension method’s first method parameter.
        - This means that the extension method’s main parameter will be passed in the method as a reference type.

### General Guidelines

- When using an extension method to extend a type that exists in a codebase that is out of your control, there is a chance that any changes to the type may break the extension method written.

### Named and Optional Arguments

- Named arguments are characterized by specifying the argument name on the method call.
    - There are rules that involves the use of named arguments which includes:
        - Only using named arguments when the arguments match the position of the parameters in terms of types.
            - In this case, you may opt to use named parameters on some or all the arguments in that method call.

- Optional arguments are characterized by specifying the default value on the argument list.
    - Optional arguments can be found at the end of the argument list of a method.

- Named and optional arguments can be used in conjunction in a scenario where most or all of the arguments in a method are optional.
    - You can target specific optional argument/s to assign to by using a named argument.
    - This scenario is typical with methods used in the Microsoft Office interop libraries.
        - Methods in these libraries oftentimes have all optional arguments.

### Overload resolution

- Here is how overload resolution works when method utilizes named and optional arguments.
    - A method becomes a candidate for execution if each of its parameter is either optional or corresponds to a singular argument in the call method statement and that argument can be converted to the type of the parameter.
    - If more than one candidate is found, overload resolution rules favor arguments that are explicitly specified.
        - Omitted arguments for optional parameters are ignored.
    - If two candidates satisfy the above criteria, preference goes to the method with the least optional arguments.
        - Overload resolution prefers candidates that have fewer parameters.

- You cannot use method overload when you have a couple methods that have the exact same signature that only differed with the modifier added to the method argument.
  
``` csharp
static int Add(ref int x) {}
static int Add(out int x) {}
```

- It is allowed to have one method use any of the method argument modifiers, and one method without the modifiers.

```csharp
static int Add(ref int x) {}
static int Add(int x) {}
```

### **Closure** ^f9967f

- A C# concept that states that a local function may continue to use variables and parameters from its outer function even if the outer function finishes its execution (by finishing the last line of code in that function, or returning a value)