# Methods

---

- In theory, a method that returns an array type can simply be a void method.
    - Since an array is a reference type, changes made on the array value that was passed in the method will also reflect on the original array variable found on the caller method.

### **Expression bodied members**

- For methods that can return a value immediately, method definitions can omit the curly brackets, `{` and `}`, and the return keyword
    - The return statement is defined with the return operator `=>`

### **Method invocation methods**

- _Positional arguments_
    - The most common.
    - Method arguments are placed in order as they are defined in the method signature.

- _Named arguments_
    - The method parameter name is included in the method call.
    - Method arguments can be in any order as long as they are all supplied in the method call.
    - Example:
    
    ```csharp
    class TestMotorcycle: Motorcycle
    {
    	public override int Drive(int miles, int speed)
    	{
    		// Implementation
    	}
    
    	static void Main(string[] args)
    	{
    		var moto = new TestMotorcycle();
    		var motoDrive = moto.Drive(speed: 170, miles: 600);
    	}
    }
    ```
    
- A method can use positional and named arguments. However, method arguments must be supplied in order.
    

### **Inherited and overridden methods**

- All types are inherited from the base class Object.
    - Types will naturally have inherited the Object methods such as Equals, `GetType` and `ToString`.

### **Parameter Arrays**

- Characterized by the `param` keyword defined on the method signature.
- Methods that have parameter array arguments can be invoked the following ways:
    - Passing an array of appropriate type
    - Passing a comma separated list of individual arguments following the same type
    - Passing null
    - Passing no arguments

### **Optional parameters and arguments**

- If a method is called using named arguments, or a combination of positional and named arguments, the method call can omit optional named arguments
- Optional parameters affect the way compilers determine which method overload is going to be executed - or otherwise known as _overload resolution_.
    - A method, indexer or constructor becomes a candidate if:
        - each of its parameter is optional or
        - each of the parameters correspond, by name or position, to a single argument in the calling statement, and that argument can be converted to the type of the parameter
    - If more than one candidate is found, it will follow the overload where all arguments are explicitly specified.
    - If there are still multiple candidates under the last filter, the overload will resolve to the candidate that has no optional arguments.