# 🏓 C# Concepts

### **Versioning**

- Uses semantic versioning
    - Characterized by the three components `[Major].[Minor].[Patch]`
        - Major - makes incompatible API changes
        - Minor - adding functionality that is backwards compatible
        - Patch - bug fixes that are backwards compatible

- Backwards compatibility strategy
    - Updating virtual methods to become non-virtual is discouraged.
        - A lot of changes will be required on the classes that create the base implementation of the method and the classes that implement an override of the same method.
    - When there’s an update in method signatures, consider adding an overload to that method.
    - The Obsolete attribute can be used to specify deprecated classes and methods.
    - Prepare for changes with methods that have optional arguments
        - Updating optional arguments into mandatory, or even updating the default value of an argument will require all those who use the method to be updated.

- It is good practice to determine values that constantly changes per update, and have those values be in the `app.config` file.
    - These prevents the hassle of re-compiling a library when it’s a simple value change.

- These are the recommended ways to consume libraries that are being constantly updated.
    - Use binding redirect on the `app.config`, specify the library and the version that will be used.
        - Recompile is not needed.
    - Add the `new` keyword to a conflicting property or method in a derived class that inherits from a base class.
        - This will hide the base class property or method in favor of the derived class property or method.
    - Using the `override` keyword.
        - This will extend the functionality of the base class to the inherited class, unlike the new keyword that hides it.

---

### Nullable value types

- Utilizes `Nullable` and `Nullable<T>` types.
    
- Features:
    - Get the underlying type of a nullable type.
    - Comparison and equality operations on two nullable types where the underlying value type does not support generic comparison and equality operations.

- Properties:    
    - `HasValue`
    - `Value`
    - If `HasValue` is false, `Value` is undefined and trying to access this property will result in `InvalidOperationException`.

- When a nullable type is boxed, CLR boxes the `Value` property of the nullable type.    
    - If the `HasValue` property is false during the time of boxing, the resulting value will be `null`.

- When unboxed, CLR will initialize a nullable type with the variable value as the underlying value.
    - If the underlying value is null, the `HasValue` property will be initialized to false.