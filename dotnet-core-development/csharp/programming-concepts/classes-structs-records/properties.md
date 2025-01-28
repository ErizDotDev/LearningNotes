# Properties

---

- Enables a class to expose a public way of getting and setting values while hiding implementing code.
- Common accessors: get, set

- New accessor introduced recently → `init`
    - This accessor indicates that a value needs to be assigned in the constructor or object initializer.
    - This is useful for enforcing object immutability - restricting modification of values inside an object.

- Properties can be added with the `required` modifier.
    - This forces client code to initialize a property or field via object initializers.
- Properties are not classified as variables.
    - Properties cannot be passed as `ref` or `out` parameter.

**[[classes-structs-records#Object Initializer|Object initializer]]**
- Term for the curly bracket notation used to initialize classes or objects.

## Using Properties

- All access modifiers are available to be used by properties.
- Property implementations in the derived class can ignore the base class’s implementation by using the `new` keyword.
- Property implementations can also be overridden given that the base class is an abstract class.

- Public and private accessors of properties can be mixed.
	- You can configure the setter as private if the property is not to be updated outside of the class.

## Interface Properties

- Like methods, interfaces can declare properties as part of the contract that a class that implements the interface needs to implement.

- It sort of acts like an abstract base class where the abstract property defines what accessors will need to be implemented and then the implementing class will implement the given accessors.
    - Note that the interface property does not have the `abstract` keyword on there.

- In a scenario where a class inherits two interfaces, both of which has property named Name, implementing the property in the class will cause a confusion in code as it will be hard to determine which interface should be called to satisfy the method implementation.    
    - Enter the concept called **[[interfaces#Explicit interface|Explicit interface implementation]]**.
        - In the implementing class, the class will call the method as such `string [IInterface1.Name](<http://IInterface1.Name>)` to specify that the property Name that was implemented belongs to `IInterface1`
        - To implement the property Name of the second interface that the class needs to implement, the property will be called as `string IInterface2.Name`

## Restricting Accessor Accessibility

- By default, the accessors follow the same visibility and access level of the property or indexer to which they belong.
    - If the property is public, the accessors of that property are also public.

- This feature allows accessors to have their own accessibility restrictions, not following the default set in the property level.    
- Typically, the common pattern is to restrict the set accessor while the get accessor remains public.
    
- Some restrictions on using accessor modifiers:
    - Cannot be used on interface or explicit interface member implementation.
    - Can be used if the property has both get and set accessors.
        - However, only one of the two is allowed to have a modifier.
    - If the property has an `override` modifier, accessor modifier must match the accessor of the overridden accessor, if any.
    - Accessibility level of the accessor modifier must be more restrictive than the accessibility level in the property.

- Hiding a property in a derived class can be achieved by setting the entire property as private.
    - The code will then look for the base class implementation of the property if it is available.

## Auto-Implemented Properties

- The notation of defining a property where no logic is written inside the get and set accessors and the keywords `get`, `set`, and `init` all are followed immediately by a semi-colon.
- Behind the scenes, the compiler creates a private, anonymous backing field that will hold the value of the properties created like this.
- Auto-implemented properties cannot be declared in interfaces.
- Auto-implemented properties can be initialized similar to fields.
    
- Here are some patterns that can be implemented to create an immutable object.
    - Create read-only properties either by:
        - Removing the set accessor; or
        - Restricting the accessibility of the set accessor
    - After creating read-only properties, implement an instance or a static method that will set the values for said properties.
        - This step will only work if the set accessor is set to private.
            - Not having the set accessor will cause a compiler error when you assign a value to the property.
                - This is because the property has no way of setting the value without the set accessor.

- You cannot create a write-only automatic property. (property that does not have a getter)